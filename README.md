# Home Lab on Mac mini 2014

A personal self-hosted infrastructure project built on Proxmox VE with a focus on virtualization, Docker, security, automation, and infrastructure best practices.

---

# Hardware

* **Host:** Mac mini (Late 2014)
* **CPU:** Intel
* **Memory:** 16 GB RAM
* **Internal Storage:** 256 GB Apple SSD
* **External Storage:** Samsung 850 PRO 2 TB (USB-SATA)

---

# Current Infrastructure

```text
Mac mini 2014
│
├── Proxmox VE
│
├── Internal SSD (256GB)
│   ├── Proxmox OS
│   └── Boot
│
├── External SSD (Samsung 850 PRO 2TB)
│   └── LVM-Thin Storage
│
└── Debian 12 Virtual Machine
    ├── Docker
    └── Portainer
```

---

# Storage Configuration

The external Samsung 850 PRO SSD has been configured as a dedicated **LVM-Thin** storage pool inside Proxmox.

### Configuration

* **Volume Group:** `vg-ssd`
* **Thin Pool:** `data`
* **Storage ID:** `ssd-storage`

### Features

* Thin Provisioning
* Virtual Machine Disks
* Container Disks
* Snapshot Support
* Clone Support

The Debian virtual machine has been successfully migrated from the default `local-lvm` storage to the new LVM-Thin storage.

---

# Virtualization

## Hypervisor

* Proxmox VE

## Virtual Machines

### Debian 12

* Docker installed
* Portainer installed
* Running from LVM-Thin storage

---

# Project Goals

This homelab is being built to learn and deploy production-style infrastructure using open-source technologies.

Planned services include:

* Nginx Proxy Manager
* Cloudflare Tunnel
* Homepage Dashboard
* Vaultwarden
* Uptime Kuma
* Monitoring
* Backup Automation
* Infrastructure Documentation

---

# Current Progress

* ✅ Proxmox VE installed
* ✅ Debian 12 virtual machine
* ✅ Docker
* ✅ Portainer
* ✅ External 2TB SSD configured
* ✅ LVM-Thin storage created
* ✅ Debian VM migrated to LVM-Thin

---

# Repository Structure

```text
Home-Lab/
├── backups/
├── compose/
├── diagrams/
├── docs/
├── images/
├── scripts/
└── README.md
```

---

# Learning Objectives

* Linux Administration
* Proxmox Virtualization
* Docker & Containers
* Infrastructure as Code
* Networking
* Reverse Proxy
* Cloud Security
* Self-hosting
* Backup & Disaster Recovery

---

# License

This repository documents my personal homelab journey and is intended for learning, documentation, and experimentation.
