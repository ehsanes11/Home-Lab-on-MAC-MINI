# Home Lab on Mac mini 2014

Personal home lab documentation for a Proxmox VE environment running on a Late 2014 Mac mini. The lab is being built as a practical learning environment for virtualization, Linux administration, Docker, secure remote access, and production-style infrastructure habits.

Last reviewed: July 2026

## Current State

The repository currently documents a single-node Proxmox VE host with dedicated VM/container storage, a Debian Docker VM, Portainer, and a Cloudflare Tunnel gateway for secure remote access.

```text
Internet
|
|-- Cloudflare Access
|-- Cloudflare Tunnel
|
Mac mini 2014
|
|-- Proxmox VE
|   |-- Internal Apple SSD 256 GB
|   |   |-- Proxmox OS
|   |   `-- boot/local storage
|   |
|   |-- Samsung 850 PRO 2 TB external SSD
|   |   `-- LVM-thin storage: ssd-storage
|   |
|   |-- Debian 12 VM
|   |   |-- Docker
|   |   `-- Portainer
|   |
|   `-- Debian LXC
|       `-- cloudflared tunnel service
```

## Hardware

| Component | Details |
| --- | --- |
| Host | Apple Mac mini, Late 2014 |
| CPU | Intel |
| Memory | 16 GB RAM |
| Internal storage | 256 GB Apple SSD |
| External storage | Samsung 850 PRO 2 TB over USB-SATA |
| Network | Wired Ethernet for Proxmox management |

## Implemented Progress

| Area | Status | Notes |
| --- | --- | --- |
| Proxmox VE | Complete | Bare-metal hypervisor installed on the Mac mini. |
| Package repositories | Complete | Enterprise and Ceph repos disabled; no-subscription repo enabled. |
| Timezone | Complete | Host timezone set to `America/New_York`. |
| Proxmox firewall | Complete | Datacenter firewall enabled. |
| External SSD storage | Complete | Samsung 850 PRO configured as an LVM-thin pool. |
| VM storage migration | Complete | Debian VM moved from default `local-lvm` to `ssd-storage`. |
| Debian 12 VM | Complete | Primary Docker host VM. |
| Docker | Complete | Installed inside the Debian VM. |
| Portainer | Complete | Installed for container management. |
| Cloudflare Tunnel | Complete | Dedicated Debian LXC runs `cloudflared`. |
| Cloudflare Access | Complete | Email authentication enabled for the exposed Proxmox endpoint. |

## Storage Configuration

The external Samsung 850 PRO SSD is configured as the primary Proxmox LVM-thin storage pool for virtual machine and container disks.

| Setting | Value |
| --- | --- |
| Volume group | `vg-ssd` |
| Thin pool | `data` |
| Storage ID | `ssd-storage` |
| Use case | VM disks, container disks, snapshots, clones |

The Debian Docker VM now runs from this LVM-thin pool instead of the default `local-lvm` storage.

## Services

### Running

- Proxmox VE
- Debian 12 VM
- Docker
- Portainer
- Dedicated Debian LXC for Cloudflare Tunnel
- Cloudflare Access-protected Proxmox web endpoint

### Planned

- Nginx Proxy Manager or another internal reverse proxy
- Homepage dashboard
- Vaultwarden
- Uptime Kuma
- Monitoring stack
- Backup automation
- GitOps or infrastructure-as-code workflow
- Additional service runbooks under `docs/`

## Documentation

- [Proxmox initial setup](docs/installation.md)
- [Cloudflare Tunnel setup](docs/cloudflare-tunnel.md)

## Repository Structure

```text
.
|-- README.md
`-- docs/
    |-- cloudflare-tunnel.md
    |-- installation.md
    `-- images/
        |-- DEVICES.jpg
        |-- Dashboard .png
        |-- first dashboard.png
        |-- proxmox-first-boot.jpg
        `-- proxmox-network-config.jpg
```

## Learning Objectives

- Proxmox virtualization
- Linux administration
- Docker and container operations
- Secure remote access with Cloudflare Zero Trust
- Storage planning for a single-node lab
- Reverse proxy and service publishing patterns
- Monitoring, backup, and recovery practices
- Clear infrastructure documentation

## Notes

This is a personal learning lab, not a production reference architecture. Public examples intentionally avoid real tunnel IDs, credentials, tokens, private IPs, and secrets.
