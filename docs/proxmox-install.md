# Proxmox VE Installation

## Purpose

This document outlines the installation of Proxmox VE as a bare-metal hypervisor for my cybersecurity homelab.

## Why Proxmox

* Enterprise-style virtualization platform
* Web-based management interface
* Efficient resource usage
* Supports VMs and containers

## Installation Steps

### 1. Create Bootable USB

* Download Proxmox VE ISO
* Use Rufus or Balena Etcher to create bootable media

### 2. Boot Installer

* Enter BIOS/UEFI
* Select USB as boot device
* Launch installer

### 3. Configuration

* Disk: Full SSD allocation
* Filesystem: ext4
* Set strong root password
* Configure email

### 4. Network Setup

* Assign static IP address
* Access interface:
  https://<IP_ADDRESS>:8006

### 5. First Login

* Username: root
* Login via web browser

## Post-Installation

### Repository Fix

* Disable enterprise repo
* Enable no-subscription repo

Update system:

* apt update
* apt upgrade

## Observations

* Lightweight and efficient
* Stable after installation
* Easy-to-use web UI

## Challenges

* Initial network access confusion
* Repository warning message

## Next Steps

* Create Kali Linux VM
* Build isolated lab network
* Deploy target machines
