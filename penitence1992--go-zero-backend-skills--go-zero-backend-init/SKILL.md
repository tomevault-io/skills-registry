---
name: go-zero-backend-init
description: Use this skill to initialize a new Go backend project using go-zero framework. It follows the directory structure and coding standards from odds-center. Supports "initialize go-zero project", "create go-zero backend", "setup new go project".
metadata:
  author: penitence1992
---

# Go Zero Backend Initialization

This skill guides the AI to initialize a new Go backend project using the go-zero framework, following the pattern of the `odds-center` reference project.

## Execution Steps

### Step 1: Gather Project Information
Ask the user for the following information if not already provided:
1. **Module Name** (e.g., `bingo` or `github.com/my/repo`)
2. **Service Name** (e.g., `user-service`)
3. **Database** (MySQL host/port/user/pass, or use defaults)
4. **Redis** (Host/port, or use defaults)

### Step 2: Create Directory Structure
Execute the following commands to create the standard directory structure:

```bash
# Create root directories
mkdir -p api/etc
mkdir -p common-service/entity
mkdir -p common-service/svc
mkdir -p infra/cache
mkdir -p infra/cfg
mkdir -p infra/convertx
mkdir -p infra/middleware
mkdir -p infra/timex
mkdir -p internal/handler
mkdir -p internal/logic
mkdir -p internal/model
mkdir -p internal/types
mkdir -p internal/svc
mkdir -p internal/config
mkdir -p etc
mkdir -p target
```

### Step 3: Generate Files from Templates
Use the `copy_from_template` pattern (conceptually) by reading the templates from `skills/go-zero-backend-init/templates/` and writing them to the project root, replacing variables.

**Variables to Replace:**
- `{{MODULE_NAME}}`: The Go module name (e.g., `bingo`)
- `{{SERVICE_NAME}}`: The service name (e.g., `user-service`)

**Files to Generate:**

1.  **go.mod**
    -   Source: `templates/go.mod.tmpl`
    -   Target: `go.mod`
    -   Action: Replace `{{MODULE_NAME}}` with module name.

2.  **Makefile**
    -   Source: `templates/Makefile.tmpl`
    -   Target: `Makefile`
    -   Action: Replace `{{SERVICE_NAME}}` if applicable.

3.  **Config (etc/{{SERVICE_NAME}}.yaml)**
    -   Source: `templates/config.yaml.tmpl`
    -   Target: `etc/{{SERVICE_NAME}}.yaml`
    -   Action: Replace database/redis configs.

4.  **Main File ({{SERVICE_NAME}}.go)**
    -   Source: `templates/main.go.tmpl`
    -   Target: `{{SERVICE_NAME}}.go`
    -   Action: Replace `{{MODULE_NAME}}`, `{{SERVICE_NAME}}`.

5.  **Service Context (internal/svc/service_context.go)**
    -   Source: `templates/svc.go.tmpl`
    -   Target: `internal/svc/service_context.go`
    -   Action: Replace `{{MODULE_NAME}}`.

6.  **Config Struct (internal/config/config.go)**
    -   Source: `templates/config_struct.go.tmpl`
    -   Target: `internal/config/config.go`
    -   Action: Replace `{{MODULE_NAME}}`.

### Step 4: Finalize
1.  Run `go mod tidy` to download dependencies.
2.  Print a summary of created files and directories.
3.  Suggest next steps (e.g., "Run 'make run' to start the server").

## Templates Location
The templates are located in the `templates/` subdirectory of this skill.

## Reference
For detailed code patterns, refer to:
- `references/code-templates.md` (Logic, Handler, Model patterns)
- `references/config-examples.md` (YAML config examples)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/penitence1992) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
