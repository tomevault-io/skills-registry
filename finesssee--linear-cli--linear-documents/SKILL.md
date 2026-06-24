---
name: linear-documents
description: Manage Linear documents. Use for creating and viewing documentation. Use when this capability is needed.
metadata:
  author: finesssee
---

# Documents

```bash
# List documents
linear-cli d list
linear-cli d list --output json

# Get document
linear-cli d get DOC_ID
linear-cli d get DOC_ID --output json

# Create document
linear-cli d create "Design Doc" -p PROJECT_ID
linear-cli d create "RFC" -p PROJECT_ID --id-only

# Update document
linear-cli d update DOC_ID --title "New Title"
linear-cli d update DOC_ID --content "New content"
```

## Flags

| Flag | Purpose |
|------|---------|
| `-p PROJECT` | Project ID |
| `--id-only` | Return ID only |
| `--output json` | JSON output |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/finesssee) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
