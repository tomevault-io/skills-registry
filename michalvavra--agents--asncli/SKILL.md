---
name: asncli
description: CLI for Asana. Manage projects and tasks. Use when this capability is needed.
metadata:
  author: michalvavra
---

# asncli

CLI for Asana via [michalvavra/asncli](https://github.com/michalvavra/asncli).

## Examples

```bash
asn auth login
asn auth status
asn config set-workspace

asn projects list [--workspace=GID] [--archived] [--limit=N]
asn projects get <gid>

asn custom-fields list [--workspace=GID] [--limit=N]

asn tasks list --project=GID [--limit=N]
asn tasks list --assignee=me [--workspace=GID] [--completed-since=RFC3339]
asn tasks get <gid>
asn tasks create --name="Task name" [--project=GID] [--assignee=me] [--notes="..."] [--due-on=YYYY-MM-DD]
asn tasks update <gid> [--name="..."] [--notes="..."] [--assignee=GID] [--due-on=YYYY-MM-DD] [--completed]

asn tasks search [--text=STRING] [--assignee-any=GID,...] [--projects-any=GID,...] [--due-on-before=YYYY-MM-DD] [--completed] [--sort-by=due_date] [--limit=N]
```

Use `--json` for parseable output, `--help` on any command for options.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/michalvavra) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
