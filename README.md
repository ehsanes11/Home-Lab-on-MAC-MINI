# Home Lab on Mac Mini

Documentation and configuration for a Home Lab running Proxmox VE on an Apple Mac Mini (Late 2014). This repo centralizes installation notes, storage recommendations, network configuration, and con[...]

Badges: (add CI / last-updated / license badges here)

Last updated: 2026-05-18

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
This repository documents a compact home lab setup for learning and running services on Proxmox VE/Ubuntu/Docker on a Mac Mini. Use it as a reference for installation, best practices, and troubles[...]

## Quickstart
1. Boot the Mac Mini from Proxmox installer media and follow the installer prompts.
2. Configure static management IP during installation.
3. After first boot: open the Proxmox web UI at `https://<MAC_MINI_IP>:8006` (or the configured hostname `https://proxmox.example.com:8006` if you've published it through DNS/Cloudflare).
4. Disable the enterprise repository (if you don't have a subscription) and add the no-subscription repository — see [Post-install Steps](#post-install-steps).
5. Download an LXC template (`debian-12-standard`) before creating containers.

## Hardware Stack
- Server: Apple Mac Mini (Late 2014)
- Hostname: proxmox.example.com
- Storage: Local SSD + 2 TB SSD
- RAM: (specify installed RAM here)
- Network: Wired Ethernet (required for stable Proxmox bridge setup)

## Software & Services
- Hypervisor: Proxmox VE (bare-metal)
- Guests / Containers: Ubuntu Server (VMs), LXC, Docker & Docker Compose
- External: Cloudflare for DNS/domain management (optional)
- Goals: Study for A+, Network+, Security+, and Linux administration

## Prerequisites
- Stable wired connection for the host during install (no Wi‑Fi for initial host networking).
- SSH access planned (enable after install).
- Router DHCP range known so you can choose an IP outside the pool.

## Proxmox Installation Notes
- Choose the wired NIC as the management interface.
- Set a meaningful FQDN for the host, e.g. `proxmox.example.com`.
- Use a static IP in your LAN subnet (e.g., `192.168.1.50/24`), gateway `192.168.1.1`, and DNS `1.1.1.1` or `8.8.8.8`.

To verify the management IP after logging into the host console:
```bash
ip a
```

Notes on the browser: Proxmox ships with a self-signed cert; your browser will warn on first access. You can replace it with a proper cert later (Let’s Encrypt via a reverse proxy is a common ap[...]

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
If you prefer to keep the enterprise file but disable it, open `/etc/apt/sources.list.d/pve-enterprise.list` and comment the `deb` line(s.

4. Update and upgrade
```bash
sudo apt update && sudo apt full-upgrade -y
```

5. Download an LXC template (Web UI)

If the Create CT wizard shows an empty Template field and returns a validation error like:

    Input error. This field is required.

it usually means there are no rootfs templates present in a directory-based storage (commonly the `local`). Proxmox lists templates that live in a directory-type storage (typically `/var/lib/vz/t[...]

Web UI (recommended for GUI users):
- Main Menu -> select your node (e.g., `pve`) -> `local` storage (or the storage name that is directory-type) -> `CT Templates` -> click the `Templates` button.
- Search for `debian-12-standard` (or the latest stable), select it, and click **Download**.
- Wait for the task to report `TASK OK`.

CLI (reliable for headless installs):
```bash
# Refresh the templates list
sudo pveam update

# (optional) List available templates and find the one you want
sudo pveam available | grep debian

# Download into the 'local' storage
sudo pveam download local debian-12-standard
```
After the download completes you should see `TASK OK` in the Web UI task log and the template will appear in the Create CT wizard once the storage view updates.

6. Connect the device to Cloudflare (optional)
   - If you manage DNS with Cloudflare, add a DNS record for the host's public hostname (A or AAAA) pointing to your router/public IP or to the IP/hostname you use to reach the host.
   - If exposing the Proxmox UI externally, prefer Cloudflare Tunnel (recommended) to avoid opening ports on your router. Cloudflare Tunnel (cloudflared) lets you securely publish services behind your[...]
   - Quick Cloudflare Tunnel steps (example on Debian/Proxmox):
     1. Install cloudflared (see Cloudflare docs for latest repo/install instructions).
     2. Authenticate: `cloudflared login` and follow the browser flow to associate the tunnel with your Cloudflare account.
     3. Create a tunnel and route a hostname: `cloudflared tunnel create pve-tunnel` then `cloudflared tunnel route dns pve-tunnel proxmox.example.com`.
     4. Run the tunnel as a service (Cloudflare docs provide a systemd service example).
   - Alternatively, add an A/AAAA record in the Cloudflare dashboard and enable the proxy (orange cloud). Be aware that some Proxmox features may require special handling if traffic is proxied.
   - Store any API tokens or keys securely if you automate DNS updates or tunnel creation.

### 🔒 Securing the Home Lab with Cloudflare Tunnel

Why Cloudflare Tunnel?

- No Public Static IP Needed: If your ISP does not provide a static public IP, Cloudflare Tunnel establishes a secure, outbound-only connection from a local environment to the Cloudflare edge net[...]
- Zero Inbound Ports Open: You do not need to configure any port forwarding on your home router. This hides your local network's public IP address from the internet and reduces exposure.
- Automatic SSL/TLS Encryption: Cloudflare automatically manages and provisions SSL certificates. All external traffic heading to local services is encrypted (HTTPS).

Network Architecture & Deployment Strategy

[ Public Internet ] ──( HTTPS )──> [ Cloudflare Edge ]
                                          │
                                   ( Cloudflare Tunnel )
                                          │
                                          ▼
                                   [ Proxmox Node ]
                             LXC Container (CT 100 - Debian 12)
                             Running cloudflared service
                                          │
                                   ( Local Network )
                                          ▼
                           [ Proxmox VE Web UI: 10.0.0.50:8006 ]

Dedicated Gateway: Deployed a lightweight Debian 12 LXC container (CT 100) on the Proxmox node (lab-node) acting as a dedicated, isolated gateway handling all tunnel traffic.

Local Ingress Proxy: The cloudflared daemon proxies incoming traffic locally over HTTPS to the Proxmox internal IP (10.0.0.50:8006), utilizing noTLSVerify: true to handle Proxmox's built-in certificat[...]

Step-by-Step Implementation

Environment Preparation:
Updated the LXC package manager and installed dependencies:

```bash
apt update && apt install curl -y
```

Repository & Client Installation:
Added the official Cloudflare package signing key and repository, then installed the cloudflared binary:

```bash
curl -L https://pkg.cloudflare.com/cloudflare-main.gpg -o /usr/share/keyrings/cloudflare-main.gpg
echo "deb [signed-by=/usr/share/keyrings/cloudflare-main.gpg] https://pkg.cloudflare.com/cloudflared debian-stable main" | tee /etc/apt/sources.list.d/cloudflared.list
apt update && apt install cloudflared -y
```

Authentication:
Authenticated the local environment with the Cloudflare Zero Trust dashboard via the generated link:

```bash
cloudflared tunnel login
```

Tunnel Configuration (redacted / example values)
Created a persistent tunnel named homelab-tunnel and structured the main system configuration file (/etc/cloudflared/config.yml). The live tunnel UUID and credentials file path have been removed [...]

```yaml
# Example (FAKE values) — do NOT commit real credentials to this repo

# Fake tunnel ID shown for demonstration only
tunnel: 00000000-0000-0000-0000-000000000000
# Fake credentials file path
credentials-file: /root/.cloudflared/00000000-0000-0000-0000-000000000000.json

ingress:
  - hostname: proxmox.example.com
    service: https://10.0.0.50:8006
    originRequest:
      noTLSVerify: true
  - service: http_status:404
```

DNS Routing & Persistence:
Mapped the custom subdomain to the established tunnel on Cloudflare's DNS, and registered cloudflared as a persistent system service:

```bash
# Create a tunnel (local name)
cloudflared tunnel create homelab-tunnel
# Route a DNS name to the tunnel (replace with your hostname)
cloudflared tunnel route dns homelab-tunnel proxmox.example.com
# Install the service with your config
cloudflared --config /etc/cloudflared/config.yml service install
systemctl start cloudflared
systemctl enable cloudflared
```

Notes and Best Practices
- Keep the cloudflared credentials file secure and do not check it into version control.
- If you use `noTLSVerify: true`, ensure the origin (Proxmox) is on a trusted local network and you understand the implications — it only disables verification of the origin certificate, not en[...]
- Prefer binding the cloudflared service to an unprivileged user inside the LXC and limit the container's network capabilities if possible.
- Test access from an external network (cellular hotspot) to confirm the tunnel is reachable and the hostname resolves to Cloudflare's edge.

### 🔐 Datacenter Two-Factor Authentication (2FA) Setup
To protect the infrastructure from unauthorized access, a global Two-Factor Authentication rule has been configured at the Datacenter level:

1. **Navigation:** Navigated to `Datacenter` -> `Two Factor` in the Proxmox VE web UI.
2. **Configuration:** Clicked **Add**, selected **TOTP**, and scanned the generated QR code using a mobile authenticator app.
3. **Verification:** Verified and linked the device (saved under description `for data center`) to enforce a 6-digit dynamic token upon every `root@pam` login.

## Missing LXC Template — Issue / Cause / Resolution

Issue:
During the LXC container creation wizard, the Template field can be empty and the UI throws a validation error:

    Input error. This field is required.

Cause:
No rootfs templates are present in the hypervisor's directory-based storage (commonly `local`). Proxmox's CT wizard lists templates that exist in directory storage (typically `/var/lib/vz/templat[...]

Resolution:
1. Download the template (Web UI) — see step 5 above.
2. Download the template (CLI): `sudo pveam update` and `sudo pveam download local debian-12-standard`.
3. Confirm the template landed in directory storage:

```bash
# Show Proxmox storages and types
pvesm status

# Check the template cache directory (local storage)
ls -lh /var/lib/vz/template/cache
```

4. If you downloaded to the wrong storage, re-download targeting the correct directory-type storage name (replace `local` with your storage's name) or reconfigure a directory-type storage.

5. Alternative automation: use `pct create` with a known rootfs tarball or automate template downloads before running the GUI wizard.

Extra troubleshooting tips:
- If `pveam download` fails: check network connectivity, DNS, and that `apt` is not blocked by repository issues (run `sudo apt update`).
- If the Web UI still doesn't show the template after a successful download: reload the Storage view, check the task log for errors, or log out and back in to the Web UI.
- Ensure there is enough free space on the storage that will host the template and the eventual container image.

Resume Container Creation
- Relaunch the `Create CT` wizard.
- Under the `Template` tab, select the directory-based storage (commonly `local`) and the newly downloaded Debian rootfs template will be available for selection.

## Common Troubleshooting

- "You do not have a valid subscription for this server" message
  - This is informational if you're using the community edition. Disable the enterprise repo and add the no-subscription repo (see above).

- `Error: command 'apt-get update' failed: exit code 100`
  - Usually caused by trying to reach the enterprise repo without a subscription. See repository fix above.

- Missing LXC templates in the wizard
  - Cause: No templates in directory storage (e.g., `local`).
  - Fix: Download a template via Web UI (Storage → local → CT Templates → Templates) or via CLI:
    ```bash
    sudo pveam update
    sudo pveam download local debian-12-standard
    ```
  - Verify: `ls -lh /var/lib/vz/template/cache` and confirm the file is present. Make sure you pick the same storage in the Create CT wizard as where the template lives.

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
