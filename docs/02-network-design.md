# Network Design

**Date:** 2026-04-08  
**Status:** Complete

## Overview

Two isolated virtual bridges enforced at the hypervisor level. No firewall rules — physical separation at the bridge layer. Lab and cloud traffic cannot communicate regardless of VM configuration.

## Bridges

| Bridge | Subnet | Internet | Purpose |
|---|---|---|---|
| `vmbr0` | DHCP (LAN) | Yes | Cloud services |
| `vmbr1` | 10.10.10.0/24 | No | Lab only |

## vmbr0 — Cloud network

```
vmbr0
├── Nextcloud LXC     personal cloud, LUKS encrypted storage
└── PiHole LXC        DNS blocking (planned)
```

Connects to the physical LAN. Services here are reachable from the home network and remotely via WireGuard once configured.

## vmbr1 — Isolated lab network

```
vmbr1 (10.10.10.0/24)
├── Kali Linux VM     10.10.10.x   attack machine
└── Target VMs        10.10.10.x   Metasploitable, VulnHub, HTB
```

No upstream gateway configured. No route to vmbr0. No internet access. Attack traffic is contained entirely within this segment.

## vmbr1 configuration

Added to `/etc/network/interfaces` on the Proxmox host:

```
auto vmbr1
iface vmbr1 inet static
        address 10.10.10.1/24
        bridge-ports none
        bridge-stp off
        bridge-fd 0
```

Applied with:

```bash
systemctl restart networking
ifup vmbr1
```

## Why bridges instead of firewall rules

A misconfigured firewall rule can be bypassed or accidentally disabled. A bridge with no upstream gateway is a hard architectural boundary — there is no network path between segments regardless of what software runs inside the VMs.

## Traffic flow diagram

```
Internet
    |
 Router
    |
  vmbr0 (LAN)
    ├── Nextcloud LXC
    └── PiHole LXC (planned)

  vmbr1 (isolated — no uplink)
    ├── Kali Linux VM
    └── Target VMs

  vmbr0 ←✗→ vmbr1   (no route between them)
```

## Planned additions

- WireGuard on Proxmox host — encrypted remote access into vmbr0
- PiHole as DNS server for all vmbr0 devices
- UFW on host — deny all inbound except ports 443 and 51820
- SSH hardening — key-only auth on port 2222
