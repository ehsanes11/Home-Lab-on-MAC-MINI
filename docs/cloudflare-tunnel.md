# Cloudflare Tunnel Setup

This document records the current Cloudflare Tunnel deployment for the Mac mini home lab.

## Goal

Expose selected self-hosted services securely without opening inbound router ports or publishing the home network's public IP address.

## Current Status

Cloudflare Tunnel is implemented and runs inside a dedicated Debian LXC container on Proxmox.

```text
Internet
|
Cloudflare Access
|
Cloudflare Tunnel
|
Dedicated Debian LXC running cloudflared
|
Proxmox VE
```

## Why This Design

- No router port forwarding required.
- Home public IP remains hidden.
- Cloudflare provides HTTPS at the edge.
- Works with dynamic residential internet.
- Reduces direct exposure of the Proxmox host.
- Keeps tunnel duties isolated from the Proxmox host and Docker VM.
- Allows Cloudflare Access policies in front of sensitive services.

## Deployment Summary

| Component | Current value |
| --- | --- |
| Runtime | Dedicated Debian LXC |
| Tunnel client | `cloudflared` |
| Protected service | Proxmox web UI |
| Origin service | `https://internal-ip-address:8006` |
| Origin TLS setting | `noTLSVerify` enabled for Proxmox self-signed certificate |
| Authentication | Cloudflare Access email authentication |
| Public example endpoint | `https://pve.example.com` |

Do not commit real tunnel IDs, credentials files, tokens, hostnames, or private IPs to this repository.

## Security Posture

Currently implemented:

- No public SSH access documented.
- No direct public exposure of Proxmox.
- No inbound router port forwarding required for the tunnel.
- Cloudflare Access enabled.
- Email authentication enabled.
- Tunnel token kept private.
- Proxmox origin remains on the local network.

Recommended next hardening:

- Require MFA in Cloudflare Access.
- Use least-privilege Cloudflare Access policies.
- Restrict the tunnel LXC to the minimum network access it needs.
- Run `cloudflared` as a service with clear restart behavior.
- Back up the tunnel configuration without committing credentials.
- Add monitoring for the tunnel service.

## Example Configuration

The live tunnel configuration must stay private. This example uses fake values.

```yaml
tunnel: 00000000-0000-0000-0000-000000000000
credentials-file: /root/.cloudflared/00000000-0000-0000-0000-000000000000.json

ingress:
  - hostname: pve.example.com
    service: https://internal-ip-address:8006
    originRequest:
      noTLSVerify: true
  - service: http_status:404
```

## Service Commands

Useful checks inside the tunnel LXC:

```bash
systemctl status cloudflared
journalctl -u cloudflared -f
cloudflared tunnel list
cloudflared tunnel info <tunnel-name>
```

## Planned Service Expansion

The tunnel currently protects the Proxmox endpoint. Future public or Access-protected routes may include:

- Portainer
- Vaultwarden
- Uptime Kuma
- Homepage dashboard
- Internal reverse proxy endpoint
- Additional Docker services

Planned target architecture:

```text
Proxmox VE
|-- cloudflared LXC
`-- Debian Docker VM
    |-- Portainer
    |-- Vaultwarden
    |-- Uptime Kuma
    `-- Reverse proxy
```

## Operational Notes

- Keep the tunnel LXC separate from the Docker VM so remote access remains available even while Docker services are being changed.
- Use Cloudflare Access for administrative tools instead of exposing them directly.
- Treat `noTLSVerify: true` as a local-origin convenience for Proxmox's self-signed certificate, not as a general default for every service.
- Prefer adding service-specific docs before exposing additional applications.
