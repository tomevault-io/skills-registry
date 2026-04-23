---
name: taskfiles
description: | Use when this capability is needed.
metadata:
  author: ionfury
---

# Taskfiles

## Repository Structure

```
Taskfile.yaml                    # Root: includes namespaced taskfiles
.taskfiles/
├── inventory/taskfile.yaml      # inv: IPMI host management
├── terragrunt/taskfile.yaml     # tg: Infrastructure operations
├── worktree/taskfile.yaml       # wt: Git worktree management
└── renovate/taskfile.yaml       # renovate: Config validation
```

## File Template

Always include schema and version:
```yaml
---
# yaml-language-server: $schema=https://taskfile.dev/schema.json
version: "3"

vars:
  MY_DIR: "{{.ROOT_DIR}}/path"

tasks:
  my-task:
    desc: Short description for --list output.
    cmds:
      - echo "hello"
```

## Patterns

### Include New Taskfiles
Add to root `Taskfile.yaml`:
```yaml
includes:
  namespace: .taskfiles/namespace
```

### Wildcard Tasks
Use for parameterized operations:
```yaml
plan-*:
  desc: Plans a specific terragrunt stack.
  vars:
    STACK: "{{index .MATCH 0}}"
  label: plan-{{.STACK}}
  cmds:
    - terragrunt plan --working-dir {{.INFRASTRUCTURE_DIR}}/stacks/{{.STACK}}
  preconditions:
    - which terragrunt
    - test -d "{{.INFRASTRUCTURE_DIR}}/stacks/{{.STACK}}"
```

### Dependencies
Run deps in parallel before cmds:
```yaml
apply-*:
  deps: [use, fmt]
  cmds:
    - terragrunt apply ...
```

### Internal Helper Tasks
```yaml
ipmi-command:
  internal: true
  silent: true
  requires:
    vars: [HOST, COMMAND]
  cmds:
    - ipmitool ... {{.COMMAND}}
```

### Preconditions
```yaml
preconditions:
  - which required-tool
  - test -d "{{.PATH}}"
  - sh: test "{{.VALUE}}" != ""
    msg: "VALUE cannot be empty"
```

### Source Tracking
```yaml
fmt:
  sources:
    - "{{.DIR}}/**/*.tf"
  generates:
    - "{{.DIR}}/**/*.tf"
  cmds:
    - tofu fmt -recursive
```

### Dynamic Variables and For Loops
```yaml
vars:
  VALID_HOSTS:
    sh: "cat {{.INVENTORY_FILE}} | yq -r '.hosts | keys[]'"

power-status:
  cmds:
    - for: { var: VALID_HOSTS }
      cmd: task inv:status-{{.ITEM}}
```

### CLI Arguments
```yaml
new:
  requires:
    vars: [CLI_ARGS]
  vars:
    NAME: "{{.CLI_ARGS}}"
  cmds:
    - git worktree add ... -b "{{.NAME}}"
```

Usage: `task wt:new -- feature-branch`

## References

- [references/task-catalog.md](references/task-catalog.md) - All available tasks by namespace
- [references/styleguide.md](references/styleguide.md) - Naming and formatting conventions
- [references/schema.md](references/schema.md) - Complete property reference
- [references/cli.md](references/cli.md) - CLI flags and options

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ionfury) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
