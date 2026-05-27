# Setup Process

## Create Tunnel

Cloudflare Zero Trust Dashboard:

```text
Networks → Tunnels → Create Tunnel
```

Tunnel type:

```text
Cloudflared
```

Tunnel name:

```text
homelab-tunnel
```

---

## Tunnel Token

Cloudflare generated a secure tunnel token used by the cloudflared service.

The token is treated as a secret and is never stored in GitHub.

---

## Deployment Method

The tunnel runs inside a dedicated Debian LXC container in Proxmox.

Container purpose:

```text
Dedicated Cloudflare Tunnel Service
```

Benefits:

- Lightweight
- Isolated from Proxmox host
- Minimal resource usage
- Persistent tunnel connection

---

## Tunnel Architecture

```text
Internet
↓
Cloudflare Access
↓
Cloudflare Tunnel
↓
Proxmox
```

---

## Security Features

- No port forwarding
- HTTPS enabled through Cloudflare
- Home public IP hidden
- Email authentication enabled
- Zero Trust access control
- Self-signed Proxmox certificate handled through Cloudflare Tunnel

---

## Tunnel Configuration

Public hostname:

```text
pve.ahmagh.shop
```

Tunnel service type:

```text
HTTPS
```

Origin service:

```text
10.0.0.60:8006
```

Additional TLS setting:

```text
No TLS Verify = Enabled
```
