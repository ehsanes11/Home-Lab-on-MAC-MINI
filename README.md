# Home Lab on Mac Mini

Documentation and configuration for my Home Lab running Proxmox VE on an Apple Mac Mini (Late 2014). This repo centralizes installation notes, storage recommendations, network configuration, and common troubleshooting steps.

Badges: (add CI / last-updated / license badges here)

Last updated: May 2026

## Table of Contents
- [Summary](#summary)
- [Quickstart](#quickstart)
- [Hardware Stack](#hardware-stack)
- [Software & Services](#software--services)
- [Prerequisites](#prerequisites)
- [Proxmox Installation Notes](#proxmox-installation-notes)
- [Storage Allocation Recommendations](#storage-allocation-recommendations)
- [Networking & IP Configuration](#networking--ip-configuration)
- [Post-install Steps](#post-install-steps)
- [Common Troubleshooting](#common-troubleshooting)
- [Files & Assets](#files--assets)
- [Contributing](#contributing)
- [License](#license)

## Summary
This repository documents a compact home lab setup for learning and running services on Proxmox VE/Ubuntu/Docker on a Mac Mini. Use it as a reference for installation, best practices, and troubleshooting.

## Quickstart
1. Boot the Mac Mini from Proxmox installer media and follow the installer prompts.
2. Configure static management IP during installation.
3. After first boot: open the Proxmox web UI at `https://<MAC_MINI_IP>:8006`.
4. Disable the enterprise repository (if you don't have a subscription) and add the no-subscription repository — see [Post-install Steps](#post-install-steps).
5. Download an LXC template (`debian-12-standard`) before creating containers.

## Hardware Stack
- Server: Apple Mac Mini (Late 2014)
- Storage: Local SSD + 2 TB SSD
- RAM: (specify installed RAM here)
- Network: Wired Ethernet (required for stable Proxmox bridge setup)

## Software & Services
- Hypervisor: Proxmox VE (bare-metal)
- Guests / Containers: Ubuntu Server (VMs), LXC, Docker & Docker Compose
- External: Cloudflare for DNS/domain management
- Goals: Study for A+, Network+, Security+, and Linux administration

## Prerequisites
- Stable wired connection for the host during install (no Wi‑Fi for initial host networking).
- SSH access planned (enable after install).
- Router DHCP range known so you can choose an IP outside the pool.

## Proxmox Installation Notes
- Choose the wired NIC as the management interface.
- Set a meaningful FQDN for the host, e.g. `pve.homelab.local` or `node1.example.com`.
- Use a static IP in your LAN subnet (e.g., `192.168.1.50/24`), gateway `192.168.1.1`, and DNS `1.1.1.1` or `8.8.8.8`.

To verify the management IP after logging into the host console:
```bash
ip a
```

Notes on the browser: Proxmox ships with a self-signed cert; your browser will warn on first access. You can replace it with a proper cert later (Let’s Encrypt via a reverse proxy is a common approach).

## Storage Allocation Recommendations
For the 2 TB SSD, consider:
- Filesystem: ext4 or XFS (single-drive setups)
- hdsize: 2000 (use total capacity)
- swapsize: 8 (GB)
- maxroot: 50 (GB)
- minfree: 16 (GB)
- Let installer allocate the remaining space to local-lvm for VM/CT images.

Consider moving long storage tuning notes to docs/storage.md.

## Networking & IP Configuration
- Use a static management IP in your router's subnet, outside DHCP.
- Example: `192.168.1.50/24`, gateway `192.168.1.1`.
- Avoid Wi‑Fi for the host when using Proxmox bridges.
- Keep a simple diagram of your network (router, switch, VLANs, port assignments).

## Post-install Steps

1. Access the Web UI
   - https://<MAC_MINI_IP>:8006

2. Disable Enterprise Repository (Web UI)
   - Select your node in the left sidebar.
   - System → Repositories.
   - Disable any `pve-enterprise` lines.
   - Add → select "No-Subscription" from Repository dropdown → Add.

3. Disable Enterprise Repository (CLI) and add no-subscription repo
   - Recommended: run as root or with sudo.
```bash
# Comment out enterprise repo
sudo sed -i 's/^deb/#deb/' /etc/apt/sources.list.d/pve-enterprise.list || true

# Add the No-Subscription repo (uses the current Debian codename)
sudo tee /etc/apt/sources.list.d/pve-no-subscription.list > /dev/null <<'EOF'
deb http://download.proxmox.com/debian/pve $(lsb_release -cs) pve-no-subscription
EOF

# Update apt
sudo apt update
```
If you prefer to keep the enterprise file but disable it, open `/etc/apt/sources.list.d/pve-enterprise.list` and comment the `deb` line(s).

4. Update and upgrade
```bash
sudo apt update && sudo apt full-upgrade -y
```

5. Download an LXC template (CLI)
```bash
sudo pveam update
sudo pveam available
sudo pveam download local debian-12-standard
```
After download, `Create CT` wizard will show the template in the `local` storage.

## Common Troubleshooting

- "You do not have a valid subscription for this server" message
  - This is informational if you're using the community edition. Disable the enterprise repo and add the no-subscription repo (see above).

- `Error: command 'apt-get update' failed: exit code 100`
  - Usually caused by trying to reach the enterprise repo without a subscription. See repository fix above.

- Missing LXC templates in the wizard
  - Download a template using the Web UI (Storage → local → CT Templates → Templates) or use `pveam` (above).

- LXC creation Input error (Template field required)
  - Download a template first (see LXC Template steps), then retry the wizard.

## Files & Assets
- docs/storage.md — advanced storage options and rationale (recommended)
- docs/networking.md — example network diagrams and VLAN steps (recommended)
- /assets — screenshots and diagrams for the README and docs (add images here)

## Contributing
- Add bug reports and feature requests as issues.
- For larger changes: open a branch (`fix/readme`), commit, and open a PR.
- Keep docs short and actionable. Move long how-tos into `docs/`.

## License
Add a license (e.g., MIT) or specify "All rights reserved" as appropriate.

---

If you want, I can:
- Commit this updated README.md to the repo or open a PR with the change.
- Split long sections into separate docs (I can create `docs/` files).
- Add scripts to automate the repository switch and template download.
