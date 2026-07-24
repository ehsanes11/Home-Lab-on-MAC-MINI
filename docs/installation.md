# Proxmox Initial Setup

This document records the initial Proxmox setup for the Mac mini home lab and reconciles the original install notes with the current lab state.

## System Information

| Item | Value |
| --- | --- |
| Device | Mac mini 2014 |
| RAM | 16 GB |
| Internal storage | 256 GB Apple SSD |
| External storage | Samsung 850 PRO 2 TB SSD |
| Hypervisor | Proxmox VE |
| Timezone | `America/New_York` |

## Current Result

The initial setup is complete. The host now runs Proxmox VE, uses the external SSD as an LVM-thin storage pool, and hosts both a Debian Docker VM and a dedicated Cloudflare Tunnel LXC.

```text
Proxmox VE
|-- Internal SSD: Proxmox OS and boot storage
|-- External SSD: LVM-thin pool named ssd-storage
|-- Debian 12 VM: Docker and Portainer
`-- Debian LXC: cloudflared tunnel
```

## Installation Checklist

- Proxmox VE installed on the Mac mini.
- Wired Ethernet used for host networking.
- Proxmox web UI verified after first boot.
- Enterprise repositories disabled because this lab does not use a paid subscription.
- Ceph repositories disabled because this is a single-node lab.
- No-subscription repository enabled.
- System upgraded successfully.
- Timezone set to `America/New_York`.
- Proxmox firewall enabled at the datacenter level.
- Debian 12 VM created.
- Docker and Portainer installed.
- Samsung 850 PRO 2 TB SSD configured as LVM-thin storage.
- Debian VM migrated to the external LVM-thin storage.
- Dedicated Debian LXC created for Cloudflare Tunnel.

## Initial System Update

Run updates after the first boot:

```bash
apt update
apt full-upgrade -y
reboot
```

## Repository Configuration

Proxmox enables enterprise repositories by default. Without a subscription, those repositories can cause authentication errors during updates.

Example error:

```text
401 Unauthorized
The repository is not signed.
```

### Disabled Repositories

- `/etc/apt/sources.list.d/pve-enterprise.sources`
- `/etc/apt/sources.list.d/ceph.sources`

Ceph was disabled because the lab is currently a single-node Proxmox environment and does not need distributed storage.

### Enabled Repository

```text
deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription
```

After changing repositories:

```bash
apt update
apt full-upgrade -y
```

## Timezone

```bash
timedatectl set-timezone America/New_York
```

## Firewall

The Proxmox firewall is enabled from:

```text
Datacenter -> Firewall -> Enable
```

## Storage Layout

### Internal SSD

The internal Apple SSD is used for:

- Proxmox OS
- Boot storage
- Local administrative storage

### External SSD

The Samsung 850 PRO 2 TB SSD has moved from "planned" to "implemented." It is configured as a dedicated Proxmox LVM-thin pool.

| Setting | Value |
| --- | --- |
| Volume group | `vg-ssd` |
| Thin pool | `data` |
| Storage ID | `ssd-storage` |

Primary uses:

- VM disks
- Container disks
- Snapshots
- Clones

## Workload Layout

### Debian 12 VM

The Debian 12 VM is the current Docker host.

Installed services:

- Docker
- Portainer

Storage:

- Migrated from default `local-lvm`
- Now running from `ssd-storage`

### Cloudflare Tunnel LXC

A dedicated Debian LXC now runs `cloudflared` for secure remote access to selected internal services. See [Cloudflare Tunnel setup](cloudflare-tunnel.md).

## Screenshots

Existing setup screenshots are stored under `docs/images/`:

- `proxmox-first-boot.jpg`
- `proxmox-network-config.jpg`
- `first dashboard.png`
- `Dashboard .png`
- `DEVICES.jpg`

## Next Steps

- Add backup strategy and restore notes.
- Add Docker Compose files after services are standardized.
- Document Portainer setup and service deployment workflow.
- Add monitoring and alerting notes.
- Document the reverse proxy decision.
