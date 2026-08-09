# Homepage Dashboard

# Goal

Homepage is used as the central dashboard for the homelab. It provides quick links to Proxmox, Jellyfin, Nginx Proxy Manager, and future services.

---

# Current Deployment

Homepage is running in Docker on the Debian VM.

```text
Proxmox node: <PROXMOX_NODE_NAME>
|
Debian VM
|
Docker
|
Homepage container: homepage
|
Config directory: /opt/homepage/config
```

Current paths:

```text
/opt/homepage/config/services.yaml
/opt/homepage/config/proxmox.yaml
```

---

# Current Dashboard Services

The working basic `services.yaml` structure is:

```yaml
- Home Lab:
    - Proxmox:
        href: "https://192.168.1.60:8006"
        description: "Virtualization Server"

    - Jellyfin:
        href: "<JELLYFIN_URL>"
        description: "Media Server"

    - Nginx Proxy Manager:
        href: "http://192.168.1.92:81"
        description: "Reverse Proxy"
```

---

# Proxmox Integration

A dedicated Proxmox user/group and API token were created for Homepage.

Intended account:

```text
homepage@pam
```

Token ID:

```text
homepage
```

The account is intended to have read-only/auditor access.

Do not commit the token secret. Use a placeholder in documentation:

```text
<PROXMOX_TOKEN_SECRET>
```

---

# API Test Result

The Proxmox API token was tested successfully from the Debian host with `curl`.

The test returned HTTP success and showed the Proxmox node:

```json
{
  "data": [
    {
      "node": "<PROXMOX_NODE_NAME>",
      "type": "node",
      "status": "online"
    }
  ]
}
```

This confirms that the API token itself can authenticate successfully from the Debian host.

---

# Current Issue

The Homepage Proxmox widget integration is not considered fully completed yet.

Known issue:

```text
Homepage previously returned HTTP 401 when trying to access:
/api2/json/cluster/resources
```

Current understanding:

- The Proxmox API token can authenticate successfully with `curl`.
- The remaining problem appears to be the Homepage widget configuration, credential format, or token formatting.
- Do not document the Homepage Proxmox widget as fully working until it is verified in the dashboard.

---

# TODO

- Verify the correct Homepage Proxmox widget credential format.
- Confirm whether the token name should be formatted as `homepage@pam!homepage`.
- Confirm the widget can access `/api2/json/cluster/resources`.
- Confirm the widget renders Proxmox resources without HTTP 401 errors.
- Document the final working widget configuration using placeholders only.

---

# Security Requirements

Never commit:

- Proxmox API token secrets
- Homepage credentials
- Passwords
- Private keys
- `.env` files containing secrets

Use placeholders such as:

```text
<PROXMOX_TOKEN_SECRET>
<HOMEPAGE_SECRET>
<JELLYFIN_URL>
<PROXMOX_NODE_NAME>
```
