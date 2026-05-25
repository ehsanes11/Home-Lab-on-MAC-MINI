# Proxmox Installation

## Hardware
- Mac mini 2014
- Intel CPU
- 16GB RAM
- 256GB Internal SSD
- 2TB External SSD (planned for backups and storage)

## Network Configuration
- Static IP configured during installation
- Gateway: 10.0.0.1
- DNS: 1.1.1.1

## Filesystem
- ext4 selected for lower RAM usage and simplicity

## Security Goals
- Cloudflare Tunnel
- Reverse Proxy
- Docker isolation
- Future VPN integration
- GitOps workflow

---

# Installation Screenshots

## Ubuntu Server Optional Snap Packages
During Ubuntu Server installation, optional snap packages such as:
- Nextcloud
- Docker
- Prometheus
- Mosquitto
were presented but skipped to keep the base system clean and lightweight.

---

## Proxmox Network Configuration
Configured:
- Static IP address
- Gateway
- DNS server
- Hostname for local infrastructure management

---

## Proxmox Final Installation Summary
Verified:
- ext4 filesystem
- Correct management interface
- Static IP assignment
- DNS and gateway configuration

---

## First Successful Boot
Proxmox VE successfully installed and accessible through:

https://10.0.0.60:8006

This marks the beginning of the homelab infrastructure project.
