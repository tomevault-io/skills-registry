---
name: kubernetes-layer
description: | Use when this capability is needed.
metadata:
  author: overthinkos
---

# kubernetes -- kubectl and Helm

## Layer Properties

| Property | Value |
|----------|-------|
| Install files | `layer.yml`, `task:` |

## Packages

RPM: `kubernetes-client`, `helm`

## Cross-distro coverage

`rpm:` (Fedora), `pac:` (Arch — `kubectl` + `helm` from `extra`), `deb:` — adds two upstream apt repos: `https://pkgs.k8s.io/core:/stable:/v1.30/deb/` for `kubectl` and `https://baltocdn.com/helm/stable/debian/all` for `helm`. Both use signed-by GPG keys. The flat-repo `pkgs.k8s.io` URL ends in `/` — supported by `build.yml`'s deb install template (the trailing-slash suite special case added during Phase 3).

## Usage

```yaml
# image.yml
my-devops:
  layers:
    - kubernetes
```

## Used In Images

- `bazzite` (disabled)

## Related Layers
- `/ov-coder:docker-ce` — common companion for local container builds
- `/ov-coder:devops-tools` — provides kubectx/kubens and cloud CLIs
- `/ov-coder:dev-tools` — typically paired in DevOps images

## Related Commands
- `/ov-build:build` — installs kubectl and helm RPMs during image build
- `/ov-core:shell` — run kubectl/helm interactively against external clusters

## When to Use This Skill

Use when the user asks about:

- Kubernetes tools in containers
- kubectl or Helm setup
- The `kubernetes` layer

## Related

- `/ov-image:layer` — layer authoring reference (`layer.yml` schema, task verbs, service declarations)
- `/ov-eval:eval` — declarative testing (`eval:` block, `ov eval image`, `ov eval live`)

---
> Source: [overthinkos/overthink-plugins](https://github.com/overthinkos/overthink-plugins) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
