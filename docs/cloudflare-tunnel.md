# Cloudflare Tunnel Setup

# Goal

Securely expose self-hosted services to the internet without opening router ports.

---

# Current Architecture

```text
Internet
↓
Cloudflare Access
↓
Cloudflare Tunnel
↓
Dedicated cloudflared LXC Container
↓
Proxmox
```

---

# Why Cloudflare Tunnel

Benefits:

* No port forwarding required
* Home public IP remains hidden
* Automatic HTTPS
* Works behind dynamic IP
* Reduced attack surface
* Secure remote access
* Zero Trust authentication

---

# Current Deployment

The tunnel currently runs inside a dedicated Debian LXC container in Proxmox.

Container purpose:

```text
Dedicated Cloudflare Tunnel Service
```

Advantages:

* Lightweight
* Isolated from Proxmox host
* Persistent connection
* Minimal resource usage

---

# Current Security Features

* No direct public exposure of Proxmox
* No public SSH access
* Cloudflare Access enabled
* Email authentication enabled
* HTTPS secured through Cloudflare
* Home IP address remains hidden
* Tunnel token remains private

---

# Public Access Endpoint

```text
https://pve.example.com
```

---

# Tunnel Configuration

Tunnel type:

```text
Cloudflared
```

Origin service:

```text
https://internal-ip-address:8006
```

TLS setting:

```text
No TLS Verify = Enabled
```

---

# Planned Future Services

Future self-hosted services may include:

* Portainer
* Vaultwarden
* Uptime Kuma
* Nginx Proxy Manager
* Additional Docker services

---

# Planned Future Architecture

```text
Proxmox
├── cloudflared LXC
└── Debian Docker VM
    ├── Portainer
    ├── Vaultwarden
    ├── Uptime Kuma
    └── Reverse Proxy
```

---

# Future Improvements

* MFA Authentication
* GitOps Integration
* Monitoring Stack
* Automated Backups
* Advanced Access Policies
* Internal Reverse Proxy
