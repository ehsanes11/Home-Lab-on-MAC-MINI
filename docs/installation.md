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

# Repository Configuration

## Why Enterprise Repositories Were Disabled

Proxmox enables enterprise repositories by default.
Since this homelab does not use a paid Proxmox subscription, the enterprise repositories generated authentication errors during updates.

Example error:

```text
401 Unauthorized
The repository is not signed.
```

---

## Enterprise Repository

Disabled:

```text
/etc/apt/sources.list.d/pve-enterprise.sources
```

---

## Ceph Repository

Disabled because:

- This is a single-node homelab
- Distributed storage is not required
- Ceph is unnecessary for this environment

Disabled file:

```text
/etc/apt/sources.list.d/ceph.sources
```

---

## No-Subscription Repository

Configured repository:

```text
deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription
```

---

## Result

System updates now work normally without authentication errors.

---

# Timezone Configuration

```bash
timedatectl set-timezone America/New_York
```

---

# Firewall

Enabled Proxmox Firewall from:

```text
Datacenter → Firewall → Enable
```

---

# Storage Layout

## Internal SSD

Used for:

- Proxmox OS
- VM disks
- Containers

---

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
