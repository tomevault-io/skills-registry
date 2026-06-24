---
name: list-projects
description: > Use when this capability is needed.
metadata:
  author: sasky80
---

# List Projects

## Platform Note

- Clean-install path: use the Go command from `.github/tools/skills-go`.

## Arguments

| # | Name | Required | Description |
|---|------|----------|-------------|
| 1 | organization | Yes | Azure DevOps organization |

## Examples

```bash
go run ./.github/tools/skills-go/cmd/skills-go list-projects myorg
```

## Output

Returns JSON with a `value` array of project objects, each containing `id`, `name`, `description`, and `state`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sasky80) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
