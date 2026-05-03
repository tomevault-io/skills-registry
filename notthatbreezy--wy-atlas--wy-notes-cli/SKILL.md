---
name: wy-notes-cli
description: > Use when this capability is needed.
metadata:
  author: notthatbreezy
---

# WY Notes CLI Skill

Use this skill when you need to automate catalogs, schemas, or entities via `wy_notes_cli`.

## Safety Rules

- Always confirm the active catalog before destructive operations.
- Always run `catalog backup` before `catalog replace`, `entity delete`, or `schema delete` if not already done.
- Prefer `--data-dir` for tests or temporary data sets.

## Common Commands

### Catalogs (Atlas)

```
wy_notes_cli catalog list
wy_notes_cli catalog use <id>
wy_notes_cli catalog export --output <path>
wy_notes_cli catalog import --input <path> --activate
wy_notes_cli catalog replace --input <path>
wy_notes_cli catalog backup
wy_notes_cli catalog restore --input <backup-path>
```

### Entities

```
wy_notes_cli entity list
wy_notes_cli entity add --schema-id <schema> --label "<label>" --data "<json>"
wy_notes_cli entity update --id <id> --label "<label>" --data "<json>"
wy_notes_cli entity delete --id <id>
```

### Schemas

```
wy_notes_cli schema list
wy_notes_cli schema upsert "<name>" --json "<schema-json>"
wy_notes_cli schema delete --id <id>
```

## AI Runbook (Preferred Flow)

1. **Inspect**:

```
wy_notes_cli catalog list
wy_notes_cli entity list
```

2. **Backup** (if destructive):

```
wy_notes_cli catalog backup
```

3. **Apply change**:

```
wy_notes_cli entity update --id <id> --label "<label>"
```

4. **Verify**:

```
wy_notes_cli entity list
```

## JSON Tips

- Use double quotes for JSON keys/strings.
- Escape quotes in shells when needed:

```
wy_notes_cli entity add --schema-id project --label "Alpha" --data "{\"title\":\"Alpha\"}"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/notthatbreezy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
