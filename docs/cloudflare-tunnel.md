# Cloudflare Tunnel Setup

# Goal

Securely expose selected self-hosted services to the internet without opening router ports or requiring a public static IP.

---

# Current Architecture

```text
Internet
|
Cloudflare
|
Cloudflare Tunnel
|
<HOMELAB_DOMAIN>
|
Nginx Proxy Manager
|
Docker services on Debian VM
|
Proxmox node: <PROXMOX_NODE_NAME>
```

---

# Current Deployment

Cloudflare Tunnel is used with a private homelab domain to expose selected homelab services.

Currently documented exposed service:

- Jellyfin: `<JELLYFIN_URL>`

The tunnel is part of the secure remote access design for the homelab. It avoids opening inbound router ports and keeps the home network from depending on a public static IP.

---

# Why Cloudflare Tunnel

Benefits:

* No port forwarding required
* Home public IP remains hidden
* Automatic HTTPS at the Cloudflare edge
* Works behind dynamic residential internet
* Reduced attack surface
* Secure remote access for selected services
* Works with Cloudflare Access policies when needed

---

# Reverse Proxy

Nginx Proxy Manager is running in Docker and is used as the reverse proxy.

Internal Nginx Proxy Manager URL:

```text
http://192.168.1.92:81
```

---

# Security Requirements

Never commit:

* Cloudflare Tunnel tokens
* Cloudflare API tokens
* Origin certificates
* Credentials files
* Passwords
* Private keys
* `.env` files containing secrets

Use placeholders in examples:

```text
<CLOUDFLARE_TUNNEL_TOKEN>
<CLOUDFLARE_API_TOKEN>
<CLOUDFLARE_ACCOUNT_ID>
<HOMELAB_DOMAIN>
<JELLYFIN_URL>
<PROXMOX_NODE_NAME>
```

---

# Planned Future Services

Future self-hosted services may include:

* Portainer
* Vaultwarden
* Uptime Kuma
* Homepage Dashboard
* Additional Docker services

---

# Future Improvements

* MFA Authentication
* GitOps Integration
* Monitoring Stack
* Automated Backups
* Advanced Access Policies
* Service-specific access policies
* Better internal service documentation
