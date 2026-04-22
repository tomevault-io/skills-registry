---
name: pdf-fixture
description: > Use when this capability is needed.
metadata:
  author: grahama1970
---

# PDF Fixture Generator

Generate deterministic test PDFs with known, extractable content for testing extraction pipelines.

**Self-contained skill** - auto-installs via `uv run` from git (no pre-installation needed).

## Simplest Usage

```bash
# Via wrapper (recommended - auto-installs)
.agents/skills/pdf-fixture/run.sh --example --name test_fixture
```

## Common Patterns

### Create from JSON spec file
```bash
./run.sh --spec content_spec.json --name my_fixture
```

### Create from inline JSON
```bash
./run.sh \
  --inline '{"sections": [{"title": "Introduction", "content": [{"type": "text", "text": "Hello world"}]}]}' \
  --name inline_test
```

## JSON Spec Format

```json
{
  "style": "standard",
  "sections": [
    {
      "title": "1. Requirements",
      "level": 1,
      "content": [
        {"type": "text", "text": "This document describes requirements."},
        {"type": "requirement", "id": "REQ-001", "text": "System shall process in 1s"},
        {"type": "table", "columns": ["ID", "Name"], "rows": [["1", "Alice"], ["2", "Bob"]]},
        {"type": "equation", "latex": "E = mc^2", "label": "energy"},
        {"type": "figure", "description": "Architecture diagram"}
      ]
    }
  ]
}
```

## Content Types

| Type | Required Fields | Optional |
|------|-----------------|----------|
| `text` | `text` | - |
| `requirement` | `id`, `text` | `type` (Functional/NonFunctional) |
| `table` | `columns` | `rows` (list or count) |
| `equation` | `latex` or `equation` | `label` |
| `figure` | `description` | `width`, `height` |
| `annotation` | `annot_type` (highlight/note/box) | `content` |

## Output

Creates in `fixtures/{name}/`:
- `source.pdf` - Generated PDF with known content
- `SPEC.md` - Auto-generated with expected extraction values

## Notes

The wrapper script (`run.sh`) automatically:
- Installs extractor from git via `uv run`
- Handles all dependencies (PyMuPDF, etc.)
- No manual venv activation needed

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/grahama1970) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
