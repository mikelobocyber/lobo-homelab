# Kali Linux VM Setup

## Purpose

This document details the setup of a Kali Linux virtual machine in Proxmox to serve as the attacker system.

## Why Kali Linux

Includes essential cybersecurity tools:

* Nmap
* Metasploit
* Burp Suite
* Wireshark

## VM Configuration

### General

* Name: kali-linux
* OS Type: Linux
* ISO: Kali Linux installer

### Resources

* CPU: 2 cores
* RAM: 2–4 GB
* Disk: 40–60 GB

## Setup Process

### 1. Upload ISO

Upload Kali ISO to Proxmox storage

### 2. Create VM

* Select ISO
* Assign CPU, RAM, disk
* Attach to vmbr0

### 3. Install OS

* Use graphical installer
* Set user credentials
* Install default tools

### 4. First Boot

* Login
* Update system:
  sudo apt update
  sudo apt upgrade

## Networking

* Connected to vmbr0 bridge
* Can communicate with other lab machines

## Verification

* Ping test successful
* Tools available
* System stable

## Next Steps

* Deploy Metasploitable
* Perform scans
* Begin exploitation practice
