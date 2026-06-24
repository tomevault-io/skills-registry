---
name: darwin-dockerfile-safety
description: Safety rules for modifying Dockerfiles and Containerfiles. Use when editing container build files. Use when this capability is needed.
metadata:
  author: the-darwin-project
---

# Dockerfile Safety Rules

## Allowed Modifications

You MAY add:

- `ARG` -- build arguments
- `ENV` -- environment variables
- `COPY` -- copy files into the image
- `RUN` -- install packages or run build commands
- `EXPOSE` -- declare ports

## Forbidden Modifications

You MUST NOT change:

- `FROM` -- base image (changing this breaks the build chain)
- `CMD` / `ENTRYPOINT` -- the container's startup command
- `USER` -- the runtime user (security context)
- `WORKDIR` -- the working directory

You MUST NOT remove:

- Existing `COPY`, `RUN`, or `CMD` lines
- Running processes from `CMD` (e.g., removing a sidecar process)

## When to Stop

If a task requires changing `FROM`, `CMD`, `USER`, or `WORKDIR`, **stop and report that it requires Architect review**. Do not proceed.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/the-darwin-project) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
