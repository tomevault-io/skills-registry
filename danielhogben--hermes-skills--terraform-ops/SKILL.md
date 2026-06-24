---
name: terraform-ops
description: Terraform IaC management skill. Use when: initializing, planning, applying, Use when this capability is needed.
metadata:
  author: Danielhogben
---

# Terraform Ops

Manage Terraform infrastructure-as-code. Supports init, plan, apply, destroy, state operations, resource imports, workspace management, validation, formatting, and output viewing.

## Usage

```bash
python3 terraform_ops.py <command> [args...]
```

## Commands

| Command | Description |
|---------|-------------|
| `init` | Initialize Terraform working directory |
| `plan` | Create an execution plan |
| `apply` | Apply changes |
| `destroy` | Destroy managed infrastructure |
| `state` | Inspect and manage state |
| `import` | Import existing resources |
| `workspace` | Manage workspaces |
| `validate` | Validate configuration |
| `fmt` | Format HCL files |
| `output` | Show output values |

## Configuration

Pass `--dir=<path>` to target a specific Terraform directory, or defaults to current working directory.

---
> Source: [Danielhogben/hermes-skills](https://github.com/Danielhogben/hermes-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
