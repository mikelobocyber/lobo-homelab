# Homelab Roadmap

## Phase 1 — Foundation
*Goal: Get Proxmox running with proper network isolation*

- [x] Install Proxmox VE on bare metal
- [x] Configure vmbr0 (LAN) and vmbr1 (isolated lab) bridges
- [x] Access Proxmox web UI
- [x] Encrypt 1TB drive with LUKS

## Phase 2 — Personal Cloud
*Goal: Self-hosted encrypted cloud storage*

- [x] Deploy Nextcloud as LXC container
- [x] Route all data to LUKS encrypted drive
- [x] Configure Apache + PHP 8.2
- [x] Set up MariaDB with least-privilege user
- [ ] WireGuard VPN — remote access
- [ ] Nginx reverse proxy + Let's Encrypt HTTPS
- [ ] Nextcloud mobile app connected
- [ ] 2FA enabled on Nextcloud admin account

## Phase 3 — Cybersecurity Lab
*Goal: Working attack and target environment*

- [x] Deploy Kali Linux 2026.1 VM on isolated network
- [ ] Deploy Metasploitable 2 as permanent target
- [ ] First nmap scan against a target
- [ ] First Metasploit session
- [ ] Complete first VulnHub machine
- [ ] Write first machine writeup
- [ ] Complete three VulnHub machines

## Phase 4 — Host Hardening
*Goal: Production-grade security on the Proxmox host*

- [ ] UFW firewall — deny all inbound by default
- [ ] SSH key-only authentication on port 2222
- [ ] Fail2ban — auto-ban on failed logins
- [ ] CrowdSec — community threat intelligence
- [ ] Automatic security updates
- [ ] PiHole LXC — network-wide DNS blocking

## Phase 5 — Skill Development
*Goal: Structured progression toward contractor-relevant skills*

- [ ] OWASP Top 10 on DVWA
- [ ] Privilege escalation practice (LinPEAS)
- [ ] Complete five VulnHub machines
- [ ] HackTheBox — first medium-difficulty machine
- [ ] CompTIA Security+ (via Army COOL)

## Phase 6 — Advanced Lab (requires RAM upgrade to 16GB+)
*Goal: Enterprise-grade practice environment*

- [ ] Wazuh SIEM — log aggregation and alerting
- [ ] VM templates (Debian, Ubuntu, Win11, WinSrv)
- [ ] Mini-GOAD — stripped Active Directory lab
- [ ] BloodHound AD enumeration
- [ ] Kerberoasting and Pass-the-Hash practice
- [ ] CompTIA CySA+
- [ ] PNPT (Practical Network Penetration Tester)

## Phase 7 — Long Term
*Goal: Multi-node cluster, full AD lab, OSCP prep*

- [ ] Second Proxmox node — clustering
- [ ] Full GOAD (Game of Active Directory)
- [ ] pfSense VM — proper lab firewall/router
- [ ] OSCP preparation
