---
name: doc-index-update
description: Maintain table of contents and index files across documentation. Use when adding, removing, or renaming documentation files. Use when this capability is needed.
metadata:
  author: reeinharddd
---

# Documentation Index Update Skill

> **Purpose:** Maintain table of contents and index files across documentation. Auto-updates when docs are added, removed, or renamed.

## Trigger

**When:** Any documentation file is created, deleted, or renamed
**Context Needed:** Changed file path, action type, existing index
**MCP Tools:** `list_dir`, `read_file`, `replace_string_in_file`

## Index Files

| Directory                          | Index File | Purpose                |
| :--------------------------------- | :--------- | :--------------------- |
| `docs/`                            | README.md  | Main documentation hub |
| `docs/technical/architecture/`     | README.md  | Architecture overview  |
| `docs/technical/backend/database/` | README.md  | Schema index           |
| `docs/technical/frontend/`         | README.md  | Frontend docs index    |
| `docs/templates/`                  | README.md  | Template guide         |

## Index Format

```markdown
# [Directory Name]

## Contents

| Document            | Type           | Status   | Description       |
| :------------------ | :------------- | :------- | :---------------- |
| [Doc Name](path.md) | feature-design | approved | Brief description |
```

## Auto-Generated Sections

### From Frontmatter

```yaml
document_type: "feature-design" # → Type column
status: "approved" # → Status column
# First line of content          # → Description
```

### Directory Tree

```markdown
## Structure
```

docs/technical/backend/
├── DATABASE-DESIGN.md
├── database/
│ ├── 01-AUTH-SCHEMA.md
│ ├── 02-BUSINESS-SCHEMA.md
│ └── ...
└── features/
└── FEAT-001-AUTH-MODULE.md

```

```

## Update Actions

| Action         | Index Change                          |
| :------------- | :------------------------------------ |
| File created   | Add row to table                      |
| File deleted   | Remove row                            |
| File renamed   | Update link                           |
| Status changed | Update status column                  |
| Moved          | Update path, possibly different index |

## Workflow

1. **Detect change** - What file changed?
2. **Find parent index** - Which README.md?
3. **Read frontmatter** - Extract metadata
4. **Update table** - Add/remove/modify row
5. **Regenerate tree** - If structure changed
6. **Save index** - Write changes

## GLOSSARY.md Sync

When new terms are introduced:

1. Check if term exists in GLOSSARY.md
2. If not, add to appropriate section
3. Maintain alphabetical order

## Cross-Reference Validation

After index update:

- [ ] All links resolve
- [ ] No orphan files (not in any index)
- [ ] Status badges accurate
- [ ] Types match actual document_type

## Reference

- [DOCUMENTATION-WORKFLOW.md](/docs/process/standards/DOCUMENTATION-WORKFLOW.md)
- [GLOSSARY.md](/docs/GLOSSARY.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/reeinharddd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
