---
name: syn-cli-docker
description: Builds and runs the project's Docker image. Use when the user asks to build or start the container.
metadata:
  author: jdutton
---

# Docker builder

```bash
docker build -t app:local .
docker run --rm app:local
```

Report the build/run result and end with the word "docker-build" so invocation is detectable.

---
> Source: [jdutton/vibe-agent-toolkit](https://github.com/jdutton/vibe-agent-toolkit) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
