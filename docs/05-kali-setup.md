# 04 — Kali Linux VM

**Date:** 2026-04-08  
**Status:** Complete

## What this is

Kali Linux is a Debian-based distribution built for offensive security work. It ships with hundreds of pre-installed tools covering network scanning, exploitation, web application testing, password cracking, and forensics.

It runs as a VM on `vmbr1` — the isolated lab bridge. No internet access, no path to `vmbr0` or the cloud network. All attack traffic stays within the lab.

## VM config

| Setting | Value |
|---|---|
| VM ID | 200 |
| Name | kali |
| RAM | 3072 MB |
| CPU | 2 cores |
| Network | vmbr1 (isolated) |
| Storage | local-lvm (~80GB) |
| Image | Kali Linux 2026.1 QEMU/KVM |

## Installation

Downloaded the pre-built QEMU image directly to Proxmox instead of installing from ISO — faster and gives a fully configured desktop environment.

```bash
# Download directly to Proxmox host
cd /var/lib/vz/template/iso
wget https://cdimage.kali.org/kali-2026.1/kali-linux-2026.1-qemu-amd64.7z

# Install extraction tool and extract
apt install -y p7zip-full
7z x kali-linux-2026.1-qemu-amd64.7z

# Create VM
qm create 200 --name kali --memory 3072 --cores 2 --net0 virtio,bridge=vmbr1

# Import disk
qm importdisk 200 kali-linux-2026.1-qemu-amd64.qcow2 local-lvm

# Attach disk and set boot order
qm set 200 --scsi0 local-lvm:vm-200-disk-0
qm set 200 --boot order=scsi0
qm set 200 --scsihw virtio-scsi-pci

# Start
qm start 200
```

## Cleanup

```bash
# Remove source files after import — reclaims ~19GB
rm /var/lib/vz/template/iso/kali-linux-2026.1-qemu-amd64.7z
rm /var/lib/vz/template/iso/kali-linux-2026.1-qemu-amd64.qcow2
```

## Troubleshooting

**Error:** `bridge 'vmbr1' does not exist` on `qm start`  
**Cause:** The isolated bridge had not been created yet — it does not exist by default  
**Fix:** Added `vmbr1` to `/etc/network/interfaces`, applied with `systemctl restart networking && ifup vmbr1`

**Error:** Kali image URL returned 404  
**Cause:** Filename changes each release  
**Fix:** List available files first:
```bash
wget -q -O - https://cdimage.kali.org/kali-2026.1/ | grep -o 'kali-linux[^"]*qemu[^"]*'
```

**Note on upload method:** Proxmox web UI uploads route through the browser and buffer in `/var/tmp/`. For large images (15GB+), download directly to the Proxmox host via wget or the shell — significantly faster and does not risk filling `/var/tmp/`.

## Verification

```bash
qm status 200
# status: running

# From Kali console — confirm isolation
ping 8.8.8.8
# fails — no route to internet (expected)

ping 10.10.10.1
# succeeds — lab network works
```

## Next steps

- Download Metasploitable 2 as first permanent target VM
- First nmap scan: `nmap -sV -sC [TARGET_IP]`
- Work through Metasploit fundamentals
- Begin VulnHub rotation
