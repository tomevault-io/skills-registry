---
name: get-file-content
description: > Use when this capability is needed.
metadata:
  author: sasky80
---

# Get File Content

## Platform Note

- Clean-install path: use the Go command from `.github/tools/skills-go`.

## Arguments

| # | Name | Required | Description |
|---|------|----------|-------------|
| 1 | organization | Yes | Azure DevOps organization |
| 2 | project | Yes | Project name or ID |
| 3 | repositoryId | Yes | Repository name or ID |
| 4 | path | Yes | Repository-relative file path in canonical form (e.g. `/src/app.js`) |
| 5 | version | No | Version string – commit SHA, branch name, or tag |
| 6 | versionType | No | Version type: `branch`, `commit`, or `tag` (default: `branch`) |

## Examples

```bash
# Get file from a specific branch
go run ./.github/tools/skills-go/cmd/skills-go get-file-content myorg MyProject MyRepo /src/app.js main branch

# Get file from a specific commit
go run ./.github/tools/skills-go/cmd/skills-go get-file-content myorg MyProject MyRepo /src/app.js abc123 commit
```

## Output

Returns JSON with `content` containing the file's text content, plus metadata like `path`, `commitId`, and `objectId`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sasky80) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
