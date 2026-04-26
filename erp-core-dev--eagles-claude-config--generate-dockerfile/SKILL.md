---
name: generate-dockerfile
description: Generate optimized multi-stage Dockerfile Use when this capability is needed.
metadata:
  author: erp-core-dev
---

# Generate Dockerfile

Generate an optimized multi-stage Dockerfile with security best practices.

## What To Do

1. **Analyze project**: Detect runtime (.NET, Node.js, Python)
2. **Generate multi-stage Dockerfile**:
   - Build stage with SDK image
   - Publish stage with runtime image
   - Non-root user
   - Health check
   - .dockerignore
3. **Security**: No secrets in image, minimal base image, non-root user

## Arguments
- `--runtime=<dotnet|node|python>`: Target runtime
- `--port=<n>`: Exposed port (default: 5000)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erp-core-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
