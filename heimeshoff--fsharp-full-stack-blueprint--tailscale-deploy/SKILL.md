---
name: tailscale-deploy
description: | Use when this capability is needed.
metadata:
  author: heimeshoff
---

# Tailscale Sidecar Deployment

## Philosophy: Private by Default

Traditional deployments expose services publicly, then add authentication. Tailscale inverts this: your application is private by default, accessible only from your tailnet. No public ports, no attack surface, no authentication to build.

**Before deploying, ask:**
- Who needs access? (Just you, your team, specific devices?)
- What's the hostname? (This becomes the access URL)
- Is persistence needed? (Volume mounts for data)
- What environment variables are required?

**Core Principles:**

1. **No Public Ports**: The application binds to internal ports only. Tailscale is the only entry point.

2. **Device-Level Auth**: Authentication happens at the Tailscale layer. If you're on the tailnet, you're authorized to access.

3. **Encrypted by Default**: All traffic between devices uses WireGuard encryption.

4. **Minimal Configuration**: One auth key, one docker-compose file. No nginx, no certbot, no firewall rules.

---

## Architecture

```
┌──────────────────────────────────────────────────┐
│                  Docker Host                      │
│                                                   │
│  ┌─────────────────┐    ┌─────────────────────┐  │
│  │   Tailscale     │    │   F# Application    │  │
│  │   Container     │───▶│     Container       │  │
│  │                 │    │                     │  │
│  │ TS_HOSTNAME:    │    │ Port: 5000          │  │
│  │ my-app          │    │ (internal only)     │  │
│  └────────┬────────┘    └─────────────────────┘  │
│           │                                       │
└───────────┼───────────────────────────────────────┘
            │
    ┌───────▼───────┐
    │   Tailnet     │
    │  (WireGuard)  │
    └───────┬───────┘
            │
    ┌───────▼───────┐
    │  Your Devices │
    │  (laptop,     │
    │   phone, etc) │
    └───────────────┘

Access: http://my-app or http://my-app:5000
```

**The pattern**: F# app runs in one container, Tailscale in another. Tailscale creates a tunnel; you access the app through that tunnel.

---

## Docker Compose Setup

### Complete docker-compose.yml

```yaml
version: '3.8'

services:
  # F# Application
  app:
    build: .
    container_name: my-fsharp-app
    restart: unless-stopped
    environment:
      - ASPNETCORE_ENVIRONMENT=Production
      - ASPNETCORE_URLS=http://+:5000
      - DATA_DIR=/app/data
    volumes:
      - ./data:/app/data
    networks:
      - app-network
    depends_on:
      - tailscale
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:5000/api/health"]
      interval: 30s
      timeout: 3s
      retries: 3
      start_period: 10s

  # Tailscale Sidecar
  tailscale:
    image: tailscale/tailscale:latest
    container_name: my-fsharp-app-tailscale
    hostname: my-fsharp-app
    restart: unless-stopped
    environment:
      - TS_AUTHKEY=${TS_AUTHKEY}
      - TS_STATE_DIR=/var/lib/tailscale
      - TS_HOSTNAME=my-fsharp-app
      - TS_ACCEPT_DNS=true
      - TS_USERSPACE=false
    volumes:
      - tailscale-data:/var/lib/tailscale
      - /dev/net/tun:/dev/net/tun
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    networks:
      - app-network
    healthcheck:
      test: ["CMD", "tailscale", "status"]
      interval: 30s
      timeout: 5s
      retries: 3

networks:
  app-network:
    driver: bridge

volumes:
  tailscale-data:
```

### Understanding the Configuration

**App Container:**
- `ASPNETCORE_URLS=http://+:5000` — Listen on all interfaces, port 5000
- `DATA_DIR` — Environment-based data directory
- `depends_on: tailscale` — Start after Tailscale is ready
- Health check verifies app is responding

**Tailscale Container:**
- `TS_AUTHKEY` — One-time key to join your tailnet
- `TS_HOSTNAME` — The name you'll use to access the app
- `/dev/net/tun` — Required for VPN tunnel
- `cap_add` — Linux capabilities for networking

---

## Setup Steps

### 1. Generate Tailscale Auth Key

1. Go to https://login.tailscale.com/admin/settings/keys
2. Click "Generate auth key"
3. Configure:
   - **Reusable**: Yes (for multiple deploys)
   - **Ephemeral**: Yes (device removed when container stops)
   - **Expiration**: 90 days (or your preference)
4. Copy the key (starts with `tskey-auth-`)

### 2. Create Environment File

```bash
# .env (in project root)
TS_AUTHKEY=tskey-auth-xxxxxxxxxxxxxx
```

**Important**: Add `.env` to `.gitignore`

### 3. Deploy

```bash
# Build and start
docker compose up -d

# Check status
docker compose ps
docker logs my-fsharp-app-tailscale
docker logs my-fsharp-app

# Verify Tailscale connection
docker exec my-fsharp-app-tailscale tailscale status
```

### 4. Access Your Application

From any device on your tailnet:
```
http://my-fsharp-app
http://my-fsharp-app:5000
```

Install Tailscale on your devices: https://tailscale.com/download

---

## Portainer Deployment

### Option 1: Using Portainer Stacks UI

1. Log in to Portainer (e.g., `http://your-server:9000`)
2. Navigate to **Stacks** → **Add stack**
3. Name: `my-fsharp-app`
4. Build method: **Web editor**
5. Paste docker-compose.yml content
6. Under **Environment variables**:
   - Add `TS_AUTHKEY` = `tskey-auth-xxxxx`
7. Click **Deploy the stack**

### Option 2: Using Git Repository

1. Push docker-compose.yml to your repo
2. In Portainer: **Stacks** → **Add stack**
3. Build method: **Repository**
4. Enter Git URL and branch
5. Add environment variables
6. Optional: Enable **Automatic updates**
7. Deploy

---

## Troubleshooting

### Tailscale Not Connecting

```bash
# Check logs
docker logs my-fsharp-app-tailscale

# Common issues:
# "no tun device" → Ensure /dev/net/tun is mounted
# "auth key invalid" → Generate new key
# "permission denied" → Check NET_ADMIN capability
```

**Fix: No TUN device**
```bash
# On host, enable TUN support
sudo modprobe tun
```

### Application Not Accessible

```bash
# Check app is running
docker logs my-fsharp-app

# Check health
docker exec my-fsharp-app curl -f http://localhost:5000/api/health

# Check network connectivity
docker exec my-fsharp-app-tailscale ping my-fsharp-app
```

### Auth Key Issues

```bash
# Key expired? Generate new one at:
# https://login.tailscale.com/admin/settings/keys

# Update .env and restart:
docker compose down
docker compose up -d
```

---

## Common Variations

### Custom Hostname

```yaml
tailscale:
  environment:
    - TS_HOSTNAME=todo-app  # Access via http://todo-app
```

### Multiple Applications

Deploy multiple apps, each with unique hostname:

```
project-a/
  docker-compose.yml  # TS_HOSTNAME=project-a

project-b/
  docker-compose.yml  # TS_HOSTNAME=project-b
```

Access:
- `http://project-a`
- `http://project-b`

### With Tailscale Serve (HTTPS)

For HTTPS without certificates:

```yaml
tailscale:
  environment:
    - TS_SERVE_CONFIG=/config/serve.json
  volumes:
    - ./serve.json:/config/serve.json
```

```json
{
  "TCP": {
    "443": {
      "HTTPS": true
    }
  },
  "Web": {
    "my-fsharp-app.your-tailnet.ts.net:443": {
      "Handlers": {
        "/": {
          "Proxy": "http://app:5000"
        }
      }
    }
  }
}
```

---

## Anti-Patterns to Avoid

❌ **Exposing Public Ports**
```yaml
# BAD: Defeats Tailscale's purpose
ports:
  - "5000:5000"
```
*Why bad*: Makes app publicly accessible, bypasses Tailscale.
*Better*: Remove port mappings. Access only via Tailscale.

❌ **Committing Auth Keys**
```yaml
# BAD: Secret in repo
environment:
  - TS_AUTHKEY=tskey-auth-xxxxx
```
*Why bad*: Auth keys in version control.
*Better*: Use `.env` file or secrets management.

❌ **Skipping Health Checks**
```yaml
# BAD: No health checks
services:
  app:
    build: .
    # No healthcheck
```
*Why bad*: Can't detect failures.
*Better*: Add health checks for monitoring.

❌ **Missing Volume Mounts for Data**
```yaml
# BAD: Data lost on container restart
services:
  app:
    build: .
    # No volumes for data
```
*Why bad*: SQLite data disappears.
*Better*: Mount `./data:/app/data`.

---

## Variation Guidance

**Development**: Skip Tailscale, use `docker compose up` with port mapping.

**Production single-user**: Single Tailscale auth key, ephemeral device.

**Team deployment**: Reusable key, Tailscale ACLs for access control.

**Multi-app server**: Separate compose files, unique hostnames.

Match the deployment complexity to your needs.

---

## Verification Checklist

Before marking deployment complete:

- [ ] docker-compose.yml created
- [ ] .env file with TS_AUTHKEY
- [ ] .env in .gitignore
- [ ] Tailscale auth key generated
- [ ] Containers start successfully
- [ ] Tailscale shows connected (`tailscale status`)
- [ ] App accessible via Tailscale hostname
- [ ] Data persists across restarts
- [ ] Health checks passing
- [ ] Logs show no errors

---

## Remember

Tailscale removes an entire category of work: no public IPs, no DNS configuration, no TLS certificates, no authentication system. Your app is simply... private. Accessible to you and your devices, invisible to everyone else.

**The goal**: Deploy once, access from anywhere on your tailnet, worry about nothing else.

## Related Documentation

- `/docs/07-BUILD-DEPLOY.md` - Docker build guide
- `/docs/08-TAILSCALE-INTEGRATION.md` - Detailed Tailscale setup
- [Tailscale Documentation](https://tailscale.com/kb/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/heimeshoff) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
