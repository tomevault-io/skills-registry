---
name: windsurf-dockerfile-generation
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---
# Windsurf Dockerfile Generation

## Overview

This skill enables AI-assisted Docker configuration within Windsurf. Cascade analyzes your application to generate optimized Dockerfiles with multi-stage builds, minimal base images, proper layer caching, and security best practices.

## Prerequisites

- Windsurf IDE with Cascade enabled
- Docker installed locally
- Application with defined dependencies
- Understanding of containerization concepts
- Target deployment environment defined

## Instructions

1. **Analyze Application**
2. **Select Base Image**
3. **Generate Dockerfile**
4. **Configure Security**
5. **Test and Validate**

See `${CLAUDE_SKILL_DIR}/references/implementation.md` for detailed implementation guide.

## Output

- Optimized production Dockerfile
- Development Dockerfile with dev tools
- docker-compose.yml for orchestration
- .dockerignore for build optimization

## Error Handling

See `${CLAUDE_SKILL_DIR}/references/errors.md` for comprehensive error handling.

## Examples

See `${CLAUDE_SKILL_DIR}/references/examples.md` for detailed examples.

## Resources

- [Windsurf Docker Guide](https://docs.windsurf.ai/features/docker)
- [Docker Best Practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
- [Container Security Guide](https://docs.windsurf.ai/guides/container-security)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
