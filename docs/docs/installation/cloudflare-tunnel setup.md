---

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

Cloudflare generated a secure tunnel token used by the cloudflared container.

The token is treated as a secret and is never stored in GitHub.

---

## Planned Deployment Method

The tunnel will run inside Docker using:

```text
cloudflare/cloudflared
```

Container location:

```text
/opt/containers/cloudflared
```

---

## Planned Routing

Traffic flow:

```text
Cloudflare
→ Tunnel
→ Reverse Proxy
→ Internal Docker Services
```
