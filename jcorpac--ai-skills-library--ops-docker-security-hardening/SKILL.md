---
name: ops-docker-security-hardening
description: Professional strategies for securing containerized applications. Use when this capability is needed.
metadata:
  author: jcorpac
---

# Docker Security Hardening

Containers are not a sandbox by default. You must secure them.

## Non-Root Execution
- **Strategy**: Always create a user (`RUN useradd ...`) and switch to them (`USER appuser`) before the `CMD`.
- **Reason**: Prevents attackers from gaining root access to the host if they compromise the container.

## Secret Management
- **Never use ENV for secrets**: Use Docker Secrets or mount secrets files at runtime.
- **Read-Only**: Mount the root filesystem as read-only whenever possible.

## Best Practices
- **Image Scanning**: Use `docker scout` or `trivy` to identy and fix vulnerabilities.
- **Resource Limits**: Set CPU and Memory limits to prevent DoS attacks on the host.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
