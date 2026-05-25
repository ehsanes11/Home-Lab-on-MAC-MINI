# Proxmox Initial Setup

## System Information

- Device: Mac mini 2014
- RAM: 16GB
- Internal Storage: 256GB SSD
- External Storage: 2TB SSD (planned for backups and storage)

---

# Initial Configuration

## Update System

```bash
apt update && apt full-upgrade -y
reboot
```

---

## Disable Enterprise Repository

Edited:

```text
/etc/apt/sources.list.d/pve-enterprise.list
```

Disabled:

```text
# deb https://enterprise.proxmox.com/debian/pve bookworm pve-enterprise
```

---

## Enable No-Subscription Repository

Added to:

```text
/etc/apt/sources.list
```

```text
deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription
```

---

## Timezone Configuration

```bash
timedatectl set-timezone America/New_York
```

---

## Firewall

Enabled Proxmox Firewall from:

Datacenter → Firewall → Enable

---

## SSH Hardening

Modified:

```text
/etc/ssh/sshd_config
```

Configured:

```text
PermitRootLogin prohibit-password
```

Restarted SSH:

```bash
systemctl restart ssh
```

---

# Storage Layout

## Internal SSD
Used for:
- Proxmox OS
- VM disks
- Containers

## External 2TB SSD
Planned for:
- Backups
- Docker data
- Media storage
- ISO images

---

# Future Plans

- Debian Docker VM
- Cloudflare Tunnel
- Reverse Proxy
- Vaultwarden
- Uptime Kuma
- GitOps workflow
- Kubernetes lab
