---
name: list-repositories
description: > Use when this capability is needed.
metadata:
  author: sasky80
---

# List Repositories

## Platform Note

- Clean-install path: use the Go command from `.github/tools/skills-go`.

## Arguments

| # | Name | Required | Description |
|---|------|----------|-------------|
| 1 | organization | Yes | Azure DevOps organization |
| 2 | project | Yes | Project name or ID |

## Examples

```bash
go run ./.github/tools/skills-go/cmd/skills-go list-repositories myorg MyProject
```

## Output

Returns JSON with a `value` array of repository objects, each containing `id`, `name`, `defaultBranch`, and `project` info.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sasky80) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
