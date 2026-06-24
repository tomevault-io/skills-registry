---
name: accept-pr
description: > Use when this capability is needed.
metadata:
  author: sasky80
---

# Accept PR

## Platform Note

- Clean-install path: use the Go command from `.github/tools/skills-go`.

## Arguments

| # | Name | Required | Description |
|---|------|----------|-------------|
| 1 | organization | Yes | Azure DevOps organization |
| 2 | project | Yes | Project name or ID |
| 3 | repositoryId | Yes | Repository name or ID |
| 4 | pullRequestId | Yes | Pull request ID |

## Examples

```bash
# Approve PR 42
go run ./.github/tools/skills-go/cmd/skills-go accept-pr myorg MyProject MyRepo 42
```

## Output

Returns JSON with the reviewer vote object including `id`, `vote`, and `displayName`.
A `vote` value of `10` means **Approved**.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sasky80) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
