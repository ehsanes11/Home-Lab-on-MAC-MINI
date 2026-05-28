# Proxmox Installation

## Hardware

* Mac mini 2014
* Intel CPU
* 16GB RAM
* 256GB Internal SSD
* 2TB External SSD (planned for backups and storage)

<img width="4032" height="3024" alt="IMG_7331 (1)" src="https://github.com/user-attachments/assets/3b5c6135-a265-4764-9b2f-dcc7901957f2" />

---

# Network Configuration

* Static IP configured during installation
* Gateway: 10.0.0.1
* DNS: 1.1.1.1

<img width="2360" height="1640" alt="IMG_0407 (1)" src="https://github.com/user-attachments/assets/4042a47a-e089-4979-b657-93ed63ae00f1" />

---

# Filesystem

* ext4 selected for lower RAM usage and simplicity

---

# Infrastructure Goals

* Secure remote access
* Cloudflare Tunnel
* Zero Trust authentication
* Docker isolation
* Future VPN integration
* Future GitOps workflow
* Self-hosted infrastructure

---

# Installation Screenshots

<img width="2360" height="1640" alt="IMG_0408" src="https://github.com/user-attachments/assets/2981956e-7f1a-4f72-95f3-82787b88b3ec" />

---

# Minimal Installation Approach

Optional packages and preconfigured services were intentionally skipped during installation to keep the system lightweight, minimal, and easier to maintain.

---

# Proxmox Network Configuration

Configured:

* Static IP address
* Gateway
* DNS server
* Local infrastructure hostname

---

# Secure Remote Access

Remote access is secured using:

* Cloudflare Tunnel
* Cloudflare Access
* Zero Trust authentication
* HTTPS encryption
* Email-based access control

No router port forwarding is used.

---

# Current Architecture

```text
Internet
↓
Cloudflare Access
↓
Cloudflare Tunnel
↓
cloudflared LXC
↓
Proxmox
```

---

# Proxmox Access

Proxmox is securely accessible through:

```text
https://pve.ahmagh.shop
```

---

# Security Notes

* No direct public exposure of Proxmox
* No public SSH exposure
* Home public IP address remains hidden
* Tunnel token is kept private
* Self-signed Proxmox certificate handled through Cloudflare Tunnel

---

# First Successful Boot

Proxmox VE successfully installed and configured.

This marks the beginning of the homelab infrastructure project.
