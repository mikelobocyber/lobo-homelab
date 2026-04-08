# 01 — Proxmox VE Setup

**Date:** 2026-04-08  
**Status:** Complete

## What this is

Proxmox VE is a bare-metal hypervisor — it runs directly on the hardware with no host OS underneath. It provides a web UI for creating and managing VMs and LXC containers, and handles all networking between them.

Installing Proxmox as the foundation means every service in this lab (Nextcloud, PiHole, Kali, future VMs) runs as an isolated guest that can be snapshotted, cloned, or destroyed without touching anything else.

## Environment

- **Host:** GMKtec G3 Pro Mini PC
- **Proxmox version:** 9.0.3
- **Kernel:** 6.17.2-1-pve
- **Node name:** wolfden

## Installation

1. Downloaded Proxmox VE ISO from proxmox.com
2. Flashed to USB with Rufus
3. Booted mini PC from USB (F7 boot menu)
4. Installed to 256GB SSD — left 1TB drive untouched for separate encrypted setup
5. Configured hostname as `wolfden`

Web UI accessible at `https://[HOST_IP]:8006` from any LAN machine.

## Network bridge configuration

Created two Linux bridges to enforce isolation between lab and cloud traffic:

| Bridge | Purpose | Internet |
|---|---|---|
| `vmbr0` | Main LAN — Nextcloud, PiHole | Yes |
| `vmbr1` | Isolated lab — Kali, target VMs | No |

`vmbr1` added manually to `/etc/network/interfaces`:

```
auto vmbr1
iface vmbr1 inet static
        address 10.10.10.1/24
        bridge-ports none
        bridge-stp off
        bridge-fd 0
```

Applied:

```bash
systemctl restart networking
ifup vmbr1
```

Any VM on `vmbr1` has no route to the internet or to `vmbr0` services. This is enforced at the hypervisor bridge level — not by firewall rules that could be misconfigured.

## Key concepts

**Why two bridges instead of a firewall rule?**  
A misconfigured firewall rule can be bypassed or accidentally disabled. A separate bridge with no upstream gateway is a hard architectural boundary — there is simply no network path between the two segments regardless of software configuration inside the VMs.

## Verification

```bash
ip a | grep vmbr1
# confirms vmbr1 up at 10.10.10.1/24

brctl show
# lists both bridges and their status
```
