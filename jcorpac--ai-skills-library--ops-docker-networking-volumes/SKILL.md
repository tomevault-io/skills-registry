---
name: ops-docker-networking-volumes
description: Managing persistent data and complex service-to-service communication. Use when this capability is needed.
metadata:
  author: jcorpac
---

# Docker Networking & Volumes

Containers are ephemeral; your data shouldn't be.

## Volumes & Persistence
- **Named Volumes**: Managed by Docker, best for databases and persistent app state.
- **Bind Mounts**: Direct host-to-container mapping, best for development code.

## Internal Networking
- **User-Defined Bridges**: Created isolated networks for containers to talk to each other via service names (DNS).
- **Aliases**: Give containers multiple network identities.

## Best Practices
- **External Networks**: Avoid using the default "bridge" network for production.
- **Volume Backups**: Regularly back up persistent volumes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
