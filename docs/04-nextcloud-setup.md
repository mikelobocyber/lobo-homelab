# 03 — Nextcloud Setup

**Date:** 2026-04-08  
**Status:** Complete

## What this is

Nextcloud is a self-hosted file sync platform — functionally equivalent to Google Drive or Dropbox, running on hardware I own. Files sync to mobile/desktop apps and the web interface is accessible from any browser.

Running it as an LXC container on Proxmox keeps resource usage low (under 512MB RAM) while maintaining isolation from other services on the host.

## Architecture

```
Proxmox host
└── Nextcloud LXC (vmbr0)
    ├── Apache2 + PHP 8.2
    ├── MariaDB
    └── /mnt/cloud/data  ← bind-mounted from LUKS encrypted 1TB
```

All user data lands on the encrypted drive. The container holds only the application.

## Container config

| Setting | Value |
|---|---|
| ID | 100 |
| Template | Ubuntu 22.04 LTS |
| Disk | 20GB (app files only) |
| RAM | 512MB |
| Network | vmbr0 |
| Unprivileged | Yes |

Bind mount the encrypted drive into the container:

```bash
pct set 100 -mp0 /mnt/cloud,mp=/mnt/cloud
```

## PHP upgrade

Ubuntu 22.04 ships PHP 8.1. Current Nextcloud requires 8.2 minimum.

```bash
add-apt-repository ppa:ondrej/php
apt update
apt install -y php8.2 php8.2-{curl,gd,zip,xml,mbstring,mysql,intl,bcmath,gmp} libapache2-mod-php8.2
a2dismod php8.1
a2enmod php8.2
systemctl restart apache2
```

This is a common real-world issue — distribution packages lag behind upstream software requirements. The fix is adding the upstream PPA maintained by the PHP packaging team.

## Database

Dedicated MariaDB user scoped only to the Nextcloud database — no root credentials used by the application:

```sql
CREATE DATABASE nextcloud;
CREATE USER 'ncuser'@'localhost' IDENTIFIED BY '[REDACTED]';
GRANT ALL PRIVILEGES ON nextcloud.* TO 'ncuser'@'localhost';
FLUSH PRIVILEGES;
```

Principle of least privilege: the application account has no access to any other database.

## Apache config

```bash
a2dissite 000-default.conf
a2ensite nextcloud.conf
a2enmod rewrite headers env dir mime
systemctl restart apache2
```

## Troubleshooting

| Error | Cause | Fix |
|---|---|---|
| Apache default page shown | Default site still enabled | `a2dissite 000-default.conf` |
| PHP 8.1 error after upgrade | Browser cache | Hard refresh with Ctrl+Shift+R / incognito window |
| `Access denied for user 'root'` | MariaDB blocks app-level root login | Use dedicated `ncuser` account |
| `Access denied for user 'ncuser' to database` | User existed but had no GRANT | `GRANT ALL PRIVILEGES ON nextcloud.*` |
| `Permission denied` creating `/mnt/cloud/data` | Bind mount read-only in unprivileged LXC | `chmod 777 /mnt/cloud` on host first |

## Verification

```bash
# Confirm encrypted drive is mounted in container
df -h /mnt/cloud
# /dev/mapper/cloud-data  916G  65M  870G  1%  /mnt/cloud

# Confirm data directory points to encrypted drive
cat /var/www/nextcloud/config/config.php | grep datadirectory
# 'datadirectory' => '/mnt/cloud/data'
```

Nextcloud dashboard accessible on the LAN. All user files written to the LUKS-encrypted 1TB drive.
