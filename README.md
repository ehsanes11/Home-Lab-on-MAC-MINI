# Proxmox Installation

## Hardware
- Mac mini 2014
- Intel CPU
- 16GB RAM
- 256GB Internal SSD
- 2TB External SSD (planned for backups and storage)
<img width="4032" height="3024" alt="IMG_7331 (1)" src="https://github.com/user-attachments/assets/3b5c6135-a265-4764-9b2f-dcc7901957f2" />

## Network Configuration
- Static IP configured during installation
- Gateway: 10.0.0.1
- DNS: 1.1.1.1
<img width="2360" height="1640" alt="IMG_0407 (1)" src="https://github.com/user-attachments/assets/4042a47a-e089-4979-b657-93ed63ae00f1" />

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
<img width="2360" height="1640" alt="IMG_0408" src="https://github.com/user-attachments/assets/2981956e-7f1a-4f72-95f3-82787b88b3ec" />

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
