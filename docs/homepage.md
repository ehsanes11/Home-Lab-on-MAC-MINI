# Homepage Dashboard

## Goal

Homepage is used as the central dashboard for the homelab. It provides quick links to Proxmox, Jellyfin, Nginx Proxy Manager, and future services.

## Current Deployment

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

Current configuration files:
/opt/homepage/config/services.yaml
/opt/homepage/config/proxmox.yaml



Current Dashboard Services
The basic services.yaml structure contains:

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




Proxmox Integration

A dedicated Proxmox user and API token were created for Homepage.

Account:

homepage@pam

Token ID:

homepage

The account is intended to have read-only/auditor access.

Never commit the token secret.

Use a placeholder in documentation:

<PROXMOX_TOKEN_SECRET>
API Test

The Proxmox API token was successfully tested from the Debian host with curl.

The API returned the Proxmox node and showed it as online.

This confirms that the API token can authenticate successfully from the Debian host.

Current Issue

The Homepage Proxmox widget is not fully working yet.

The Debian host can successfully authenticate against:

/api2/json/cluster/resources

However, Homepage previously returned:

HTTP 401

when accessing the same Proxmox API endpoint.

Therefore, the Homepage Proxmox widget should not be considered fully configured until it is verified in the dashboard.

Troubleshooting Status

Current findings:

Proxmox API connectivity works.
API token authentication works with curl.
Homepage can reach the Proxmox API endpoint.
Homepage widget authentication/configuration still requires troubleshooting.
TODO
Verify the correct Homepage Proxmox widget credential format.
Verify token formatting.
Confirm the widget can access /api2/json/cluster/resources.
Confirm the widget displays Proxmox resources without HTTP 401 errors.
Document the final working configuration using placeholders only.
Security

Never commit:

Proxmox API token secrets
Homepage credentials
Passwords
Private keys
.env files containing secrets

Use placeholders such as:

<PROXMOX_TOKEN_SECRET>
<HOMEPAGE_SECRET>
<JELLYFIN_URL>
<PROXMOX_NODE_NAME>
