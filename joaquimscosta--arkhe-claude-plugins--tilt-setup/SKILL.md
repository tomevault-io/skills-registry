---
name: tilt-setup
description: >- Use when this capability is needed.
metadata:
  author: joaquimscosta
---

# Tilt Setup

Install [Tilt](https://tilt.dev/) and scaffold or audit `Tiltfile` + `tilt/` configurations for local Kubernetes development with ecosystem-aware templates.

## Pre-flight

Run the detection script to understand current state:
```bash
python3 ${CLAUDE_SKILL_DIR}/scripts/detect_tilt.py <project-root>
```

The detector reports: tilt/kubectl binaries, current kubectl context (with production-pattern matching), existing Tiltfile and `tilt/` modular layout (also recognizes legacy `.tilt/`), `.tiltignore`, `tilt_config.json`, ecosystems detected (Java/Gradle, Next.js, Python, infra), local cluster tools available, and audit violations (`TILT001`–`TILT025`) when a Tiltfile exists.

## Decision Flow

```
Run detector
    |
    ├── tilt_binary.installed = false → Install Tilt first
    |
    ├── kubectl_context.is_production_pattern = true → STOP and warn user
    |
    ├── tiltfile.exists = true → Phase 1: Audit
    |
    └── tiltfile.exists = false → Phase 2: Scaffold
```

## Phase 1: Audit (Existing Tiltfile)

1. **Summarize findings** — show a status table:

   | Component | Status | Detail |
   |-----------|--------|--------|
   | tilt binary | installed/missing | version, path |
   | kubectl context | safe/production | current context, classification |
   | Tiltfile | found/not found | path, line count |
   | Modular layout | yes/no | `tilt/` files (config.star, services.star, *.yaml) |
   | Ecosystems | N detected | java-gradle, nextjs, python, infra |
   | Service count | N services | `k8s_resource` calls |
   | Safety guard | yes/no | `allow_k8s_contexts` or manual guard |
   | .tiltignore | yes/no | path |

2. **Present audit violations** grouped by severity (ERROR > WARNING > INFO):
   - Show rule ID, message, fix hint
   - Rules cover: missing safety guard, deprecated `restart_container`, missing `live_update`, no `watch_settings`, no `update_settings`, missing `resource_deps`/`labels`/`port_forwards`, monolithic Tiltfile, etc.

3. **Use `AskUserQuestion`** (multiSelect: true) — ask which violations to fix.

4. **Apply selected fixes** — see [WORKFLOW.md](WORKFLOW.md) for per-rule fix strategies.

5. **Re-run detector** to verify fixes were applied.

## Phase 2: Scaffold (No Tiltfile)

1. **Install `tilt`** if missing — show commands based on `os` field:
   - **macOS**: `brew install tilt-dev/tap/tilt`
   - **Linux**: `curl -fsSL https://raw.githubusercontent.com/tilt-dev/tilt/master/scripts/install.sh | bash`
   - Verify: `tilt version`

2. **Verify kubectl context is safe** — if `kubectl_context.is_production_pattern` is true, STOP and ask the user to switch contexts before proceeding.

3. **Review detected ecosystems** — show what was found.

4. **Choose Tiltfile pattern** — use `AskUserQuestion`:
   - **Single-file** (`Tiltfile` only) — recommended for 1–3 services, single ecosystem
   - **Modular** (`Tiltfile` + `tilt/config.star` + `tilt/services.star` + `tilt/service-config.yaml` + `tilt/environments.yaml`) — recommended for 4+ services, 2+ ecosystems, or 2+ environment presets

5. **Choose features** — use `AskUserQuestion` (multiSelect: true):

   | Feature | When to enable |
   |---------|----------------|
   | Manual context guard | Always (recommended over `allow_k8s_contexts` alone) |
   | PVC persistence toggle | Stateful services (postgres, kafka, opensearch) |
   | JDWP debug ports | Java/Spring Boot services |
   | Monitoring stack | Prometheus + Grafana via `helm_resource` |
   | Traefik gateway | Multi-service projects with HTTP routing |

6. **Generate Tiltfile + supporting files** — see [WORKFLOW.md](WORKFLOW.md) for templates.

   Always include in the root Tiltfile:
   - Manual `validate_cluster_safety()` guard at top (blocks `arn:aws:eks:`, `gke_`, `prod`, `production`, `staging`)
   - `watch_settings(ignore=[...])` with build artifacts and dep dirs
   - `update_settings(max_parallel_updates=2)` for laptop-friendly CPU usage
   - `.tiltignore` next to Tiltfile

7. **Verify** — run `tilt alpha tiltfile-result` to validate syntax, then re-run detector.

## Key Rules

- **Never overwrite** existing `Tiltfile` or `tilt/` files without asking. Offer merge/replace/skip.
- **Detect first** — skip steps already configured.
- **Use `AskUserQuestion`** for every decision. Do not assume user preferences.
- **Always start with safety guard** — refuse to scaffold without one. The cost of accidental prod deployment is too high.
- **Use `ext://restart_process` not `restart_container()`** — the latter is deprecated.
- **Load extensions only from root Tiltfile** — Starlark `load()` of `ext://` from sub-files fails.
- **Externalize service definitions to YAML** for modular layouts — `read_yaml("tilt/service-config.yaml")` keeps config team-editable.
- **PVC persistence pattern** — create persistent PVCs via `local("kubectl apply")` outside Tilt's lifecycle so they survive `tilt down`.
- **Per-ecosystem live_update**:
  - Spring Boot: `custom_build` + `local_resource` compile + `sync` of `.class` files
  - Next.js: `local_resource` with `serve_cmd` (preferred) OR container with `WATCHPACK_POLLING=true`
  - Python: `docker_build` + `sync` + uvicorn `--reload` (no `restart_container` needed)

## Reference Material

The skill's reference base lives at `docs/research/tilt-local-kubernetes-development-setup.md` (in this repo). It contains the full Tilt API reference, ecosystem recipes, cluster comparison, audit rule definitions (TILT001–TILT025), and scaffold templates.

## References

- **Workflow**: See [WORKFLOW.md](WORKFLOW.md) for detailed per-step flows and templates
- **Examples**: See [EXAMPLES.md](EXAMPLES.md) for example setup and audit sessions
- **Troubleshooting**: See [TROUBLESHOOTING.md](TROUBLESHOOTING.md) for common issues
- **Detection Script**: See [scripts/detect_tilt.py](scripts/detect_tilt.py) for detection logic
- **Research base**: `docs/research/tilt-local-kubernetes-development-setup.md`

---
> Source: [joaquimscosta/arkhe-claude-plugins](https://github.com/joaquimscosta/arkhe-claude-plugins) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
