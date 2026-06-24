---
name: docker
description: Accelerate how you build, share, and run applications Use when this capability is needed.
metadata:
  author: sami
---

# Docker Skill

## Best Practices
1.  **Multi-Stage Builds**: Use a builder stage to compile, and a runner stage (Alpine/Slim) for the final image to reduce size.
2.  **Caching**: Order usage matters. Copy `package.json` and install deps BEFORE copying source code to leverage layer caching.
3.  **User**: Don't run as `root`. Create a dedicated user in the Dockerfile.

## Common Pitfalls
*   **Secrets**: Baking secrets/env vars into the image. Use Env Vars at runtime.
*   **Latest Tag**: Pin versions (`node:18`, not `node:latest`) to avoid breaking changes.

## References
*   [Dockerfile Best Practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sami) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
