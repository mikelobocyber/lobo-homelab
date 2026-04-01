# Network Design

## Overview

This homelab uses a virtualized network environment within Proxmox to simulate a controlled cybersecurity testing environment.

## Architecture

* Proxmox Host (bare-metal hypervisor)
* Kali Linux VM (attacker machine)
* Metasploitable VM (target machine)

## Network Configuration

* Bridge: vmbr0
* Type: Internal virtual network
* Purpose: Isolated environment for safe testing

## Design Goals

* Ensure all testing is contained within a controlled network
* Simulate real-world attack scenarios
* Maintain separation from external networks

## Future Improvements

* VLAN segmentation
* Firewall integration (pfSense)
* Multi-network architecture
