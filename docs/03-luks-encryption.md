# 02 — LUKS Full Disk Encryption

**Date:** 2026-04-08  
**Status:** Complete

## What this is

LUKS (Linux Unified Key Setup) encrypts the entire 1TB drive at the block level. Every byte written to the drive is encrypted before it hits the physical disk. Without the correct key, the data is indistinguishable from random noise — physically removing the drive yields nothing readable.

This is the first operation performed on the 1TB drive before any data is written to it.

## Why this matters

Full disk encryption protects data at rest. If the hardware is stolen or accessed physically, the storage is unreadable without the key. This is a baseline requirement for anything handling personal data and maps directly to concepts tested in Security+ (domain: Implementation).

## Commands

```bash
# Identify the 1TB drive — confirm before proceeding
lsblk

# Format with LUKS encryption
cryptsetup luksFormat /dev/nvme1n1

# Open the encrypted volume
cryptsetup luksOpen /dev/nvme1n1 cloud-data
# drive now accessible at /dev/mapper/cloud-data

# Create filesystem on the decrypted volume
mkfs.ext4 /dev/mapper/cloud-data

# Mount
mkdir /mnt/cloud
mount /dev/mapper/cloud-data /mnt/cloud

# Create a keyfile for auto-unlock on boot
dd if=/dev/urandom of=/etc/cloud-keyfile bs=512 count=4
chmod 400 /etc/cloud-keyfile
cryptsetup luksAddKey /dev/nvme1n1 /etc/cloud-keyfile
```

`/etc/crypttab`:
```
cloud-data  /dev/nvme1n1  /etc/cloud-keyfile  luks
```

`/etc/fstab`:
```
/dev/mapper/cloud-data  /mnt/cloud  ext4  defaults  0  2
```

## Troubleshooting

**Error:** `The file dev/mapper/cloud-data does not exist`  
**Cause:** Missing leading `/` — used a relative path instead of absolute  
**Fix:** All device and mount paths must start with `/`

**Root cause analysis:** Linux resolves paths without a leading `/` relative to the current working directory. `/dev/mapper/` is an absolute path from the root filesystem. `dev/mapper/` looks for a `dev` directory in wherever you currently are — which does not exist.

## Verification

```bash
df -h /mnt/cloud
# /dev/mapper/cloud-data  916G  65M  870G  1%  /mnt/cloud

cryptsetup status cloud-data
# confirms cipher: aes-xts-plain64, keysize: 512 bits
```

916GB available — correct for a 1TB drive after LUKS header and filesystem overhead.
