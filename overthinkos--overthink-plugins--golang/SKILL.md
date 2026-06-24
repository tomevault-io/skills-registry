---
name: golang
description: | Use when this capability is needed.
metadata:
  author: overthinkos
---

# golang -- Go compiler

## Layer Properties

| Property | Value |
|----------|-------|
| Install files | `layer.yml` (packages only) |

## Packages

RPM: `golang-bin` · PAC: `go` · DEB: `golang-go` — full cross-distro parity.

## Usage

```yaml
# image.yml
my-image:
  layers:
    - golang
```

## Used In Images

- `aurora` (disabled)

## Related Skills

- `/ov-coder:language-runtimes` -- also includes `golang-bin` alongside other runtimes
- `/ov-coder:rust`, `/ov-coder:nodejs` — sibling language runtimes
- `/ov-coder:build-toolchain` — C/C++ toolchain often paired with Go builds
- `/ov-distros:github-runner` — consumes go for Actions workflows
- `/ov-image:layer` — layer authoring
- `/ov-internals:go` — ov CLI is itself built from Go — development conventions

## When to Use This Skill

Use when the user asks about:

- Go compiler in containers
- Go development environment
- The `golang` layer

## Related

- `/ov-eval:eval` — declarative testing (`eval:` block, `ov eval image`, `ov eval live`)

---
> Source: [overthinkos/overthink-plugins](https://github.com/overthinkos/overthink-plugins) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
