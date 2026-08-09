# Proxmox Initial Setup

## System Information

- Device: Mac mini 2014
- Proxmox node name: `<PROXMOX_NODE_NAME>`
- Proxmox IP: `192.168.1.60`
- Proxmox Web UI: `https://192.168.1.60:8006`
- RAM: 16 GB
- Internal Storage: 256 GB SSD
- External Storage: Samsung 850 PRO 2 TB SSD

---

# Current Result

Proxmox VE is installed directly on the Mac mini. The node name is documented as `<PROXMOX_NODE_NAME>` and is available on the local network at `192.168.1.60`.

Current workload layout:

```text
Proxmox VE node: <PROXMOX_NODE_NAME>
|
|-- Internal SSD
|   |-- Proxmox OS
|   `-- Boot
|
|-- External Samsung 850 PRO 2 TB SSD
|   `-- LVM-Thin storage
|
`-- Debian VM
    |-- Docker
    |-- Docker Compose
    |-- Portainer
    |-- Homepage
    |-- Jellyfin
    `-- Nginx Proxy Manager
```

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

Proxmox enables enterprise repositories by default. Since this homelab does not use a paid Proxmox subscription, the enterprise repositories generated authentication errors during updates.

Example error:

```text
401 Unauthorized
The repository is not signed.
```

## Enterprise Repository

Disabled:

```text
/etc/apt/sources.list.d/pve-enterprise.sources
```

## Ceph Repository

Disabled because:

- This is a single-node homelab
- Distributed storage is not required
- Ceph is unnecessary for this environment

Disabled file:

```text
/etc/apt/sources.list.d/ceph.sources
```

## No-Subscription Repository

Configured repository:

```text
deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription
```

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
Datacenter -> Firewall -> Enable
```

---

# Storage Layout

## Internal SSD

Used for:

- Proxmox OS
- Boot
- Local administrative storage

## External 2 TB SSD

The external Samsung 850 PRO 2 TB SSD is configured as dedicated LVM-Thin storage for VM and container disks.

Configuration:

- Volume Group: `vg-ssd`
- Thin Pool: `data`
- Storage ID: `ssd-storage`

Used for:

- VM disks
- Container disks
- Snapshots
- Clones

---

# Debian VM

The Debian VM is the main Docker host.

Installed:

- Docker
- Docker Compose
- Portainer

Current Docker services include:

- Homepage
- Jellyfin
- Nginx Proxy Manager

---

# Screenshots

Existing setup screenshots are stored under `docs/images/`:

- `proxmox-first-boot.jpg`
- `proxmox-network-config.jpg`
- `first dashboard.png`
- `Dashboard .png`
- `DEVICES.jpg`

---

# Future Plans

- Complete Homepage Proxmox widget troubleshooting
- Add service-specific Docker Compose documentation
- Add backup automation
- Add monitoring
- Add Vaultwarden
- Add GitOps workflow
