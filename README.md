# 🐺 Lobo Cybersecurity Homelab
 
## Overview

This repository documents my personal cybersecurity homelab built on Proxmox VE. The goal is to develop hands-on skills in virtualization, networking, penetration testing, and system administration through real-world lab environments — built from scratch on bare metal.
 
## Hardware

- GMKtec G3 Pro Mini PC
- Intel Core i3-10110U
- 8GB DDR4 RAM
- 256GB SSD (boot + lab VMs)
- 1TB M.2 NVMe (personal cloud — LUKS encrypted)

## Architecture

```
Proxmox VE (bare metal)
├── vmbr0 — main LAN (internet access)
│   ├── Nextcloud LXC    personal cloud, data on LUKS encrypted 1TB
│   └── PiHole LXC       DNS-level network-wide blocking (planned)
└── vmbr1 — isolated lab network (no internet, no cloud access)
    └── Kali Linux VM    attack machine
        └── Target VMs   Metasploitable 2, VulnHub, HackTheBox
```

Lab and cloud traffic run on separate bridges enforced at the hypervisor level. Attack traffic generated during practice has no path to the cloud network or personal data.

## Stack

| Component | Technology |
|---|---|
| Hypervisor | Proxmox VE 9.0 |
| Personal cloud | Nextcloud (LXC) |
| Storage encryption | LUKS full disk |
| VPN | WireGuard (planned) |
| Reverse proxy | Nginx + Let's Encrypt (planned) |
| DNS blocking | PiHole (planned) |
| Attack machine | Kali Linux 2026.1 |
| Firewall | UFW (planned) |
| Intrusion prevention | Fail2ban + CrowdSec (planned) |

## Build Log

| Date | Milestone |
|---|---|
| 2026-04-08 | Proxmox VE installed, network bridges configured |
| 2026-04-08 | 1TB drive encrypted with LUKS |
| 2026-04-08 | Nextcloud deployed as LXC, data routed to encrypted drive |
| 2026-04-08 | Kali Linux 2026.1 imported and running on isolated network |

## Docs

- [Proxmox Setup](docs/01-proxmox-setup.md)
- [Network Design](docs/02-network-design.md)
- [LUKS Disk Encryption](docs/03-luks-encryption.md)
- [Nextcloud Setup](docs/04-nextcloud-setup.md)
- [Kali Linux VM](docs/05-kali-setup.md)

## Roadmap

- [x] Install Proxmox VE
- [x] Configure isolated network bridges (vmbr0 / vmbr1)
- [x] Encrypt 1TB drive with LUKS
- [x] Deploy Nextcloud on encrypted storage
- [x] Deploy Kali Linux VM on isolated network
- [ ] WireGuard VPN for remote access
- [ ] PiHole DNS blocking
- [ ] UFW + Fail2ban + CrowdSec host hardening
- [ ] SSH key-only authentication
- [ ] Metasploitable 2 target VM
- [ ] First VulnHub machine + writeup
- [ ] Wazuh SIEM (requires RAM upgrade)
- [ ] Active Directory lab with GOAD (requires RAM upgrade)

## Objectives

- Build and manage a production-style virtualized lab
- Practice offensive and defensive security in isolated environments
- Develop Linux and networking fundamentals through hands-on work
- Document everything as it is built — including what breaks and how it gets fixed

## Notes

All testing is performed in isolated environments I own and control, using intentionally vulnerable machines designed for this purpose.

## About

Computer Science Student | Oklahoma Army National Guard | Cybersecurity Focus
