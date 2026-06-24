---
name: post-pr-comment
description: > Use when this capability is needed.
metadata:
  author: sasky80
---

# Post PR Comment

## Platform Note

- Clean-install path: use the Go command from `.github/tools/skills-go`.

## Arguments

| # | Name | Required | Description |
|---|------|----------|-------------|
| 1 | organization | Yes | Azure DevOps organization |
| 2 | project | Yes | Project name or ID |
| 3 | repositoryId | Yes | Repository name or ID |
| 4 | pullRequestId | Yes | Pull request ID |
| 5 | filePath | Yes | Repository-relative file path for inline comment (canonical form like `/src/app.js`; use `-` for a general comment) |
| 6 | line | Yes | Line number for inline comment (use `0` for general comments) |
| 7 | comment | Yes | Comment text (supports Markdown) |

## Examples

```bash
# Inline comment on a specific file and line (canonical repository path)
go run ./.github/tools/skills-go/cmd/skills-go post-pr-comment myorg MyProject MyRepo 42 /src/app.js 15 "Consider using const here."

# General PR-level comment
go run ./.github/tools/skills-go/cmd/skills-go post-pr-comment myorg MyProject MyRepo 42 - 0 "Overall the code looks good."
```

## Formatting Note

When posting structured review findings, avoid literal `\n\n` sequences in comment text.
Use a single HTML line break (`<br/>`) between sections, for example:

```text
🟠 Major | Security<br/>Description: ...<br/>Recommendation: ...
```

## Output

Returns JSON with the created thread object including `id`, `comments`, and `status`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sasky80) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
