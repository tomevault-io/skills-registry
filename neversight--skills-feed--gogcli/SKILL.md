---
name: gogcli
description: CLI for Gmail, Calendar, Drive, Contacts, Tasks, Sheets. Use when this capability is needed.
metadata:
  author: neversight
---

# gogcli

CLI for Google Workspace via [steipete/gogcli](https://github.com/steipete/gogcli).

## Examples

```bash
gog gmail search 'newer_than:7d from:x@example.com'
gog gmail thread <threadId>
gog gmail send --to x@example.com --subject "Hi" --body "Text"

gog calendar events primary --from 2025-01-15T00:00:00Z --to 2025-01-16T00:00:00Z
gog calendar create primary --summary "Meeting" --from <RFC3339> --to <RFC3339>

gog drive search "quarterly report"
gog drive upload ./file.pdf --parent <folderId>
gog drive download <fileId> --out ./file.pdf

gog tasks list <tasklistId>
gog tasks add <tasklistId> --title "Task" --due <RFC3339>

gog contacts search "John"

gog sheets get <spreadsheetId> 'Sheet1!A1:B10'
```

Use `--json` for parseable output, `--help` on any command for options.

---

See [references/examples.md](references/examples.md) for Gmail query syntax, batch operations, workflows.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
