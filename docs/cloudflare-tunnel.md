# Cloudflare Tunnel Setup

# Goal

Securely expose self-hosted services to the internet without opening router ports.

---

# Architecture

```text
Internet
   ↓
Cloudflare
   ↓
Encrypted Tunnel
   ↓
cloudflared container
   ↓
Reverse Proxy
   ↓
Docker Services
```

---

# Why Cloudflare Tunnel

Benefits:

- No port forwarding required
- Real home IP remains hidden
- Automatic HTTPS
- Works behind dynamic IP
- Reduced attack surface
- Easy remote access

---

# Planned Services

The tunnel will later expose:

- Portainer
- Vaultwarden
- Uptime Kuma
- Future self-hosted services

---

# Security Notes

- No direct public exposure of Proxmox
- No public SSH access
- Tunnel token must remain private
- Reverse proxy will be used for routing

---

# Planned Stack

```text
Proxmox
   ↓
Docker VM
   ↓
Docker Containers
   ├── cloudflared
   ├── nginx proxy manager
   ├── portainer
   ├── vaultwarden
   └── uptime kuma
```

---

# Tunnel Type

Cloudflare Zero Trust Tunnel using:

```text
cloudflared
```

---

# Future Improvements

- Access Policies
- MFA Authentication
- Geo Blocking
- WAF Rules
- GitOps Integration
