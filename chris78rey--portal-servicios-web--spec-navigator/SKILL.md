---
name: spec-navigator
description: Efficiently retrieve specific sections from requirement documents (prompts) to save context tokens. Use when this capability is needed.
metadata:
  author: chris78rey
---

# Spec Navigator Skill

This skill allows you to query specific sections of the master instruction file (`inst.md`) or any markdown file, saving you from reading the entire file into the context window of the LLM.

## Purpose

To significantly reduce token consumption when working with large specification files like `inst.md` by only loading the relevant section (e.g., "Architecture", "Stack", "MVP") into the conversation context.

## Available Tools

### `scripts/query_md_section.py`

Extracts a section and its subsections from a markdown file based on a header search term.

**Usage:**

```bash
python3 .agent/skills/spec_navigator/scripts/query_md_section.py [ABSOLUTE_PATH_TO_FILE] [HEADER_SEARCH_TERM]
```

**Workflow Example:**

1.  User asks: "What is the database configuration?"
2.  Instead of reading the whole `inst.md`, run:
    ```bash
    python3 .agent/skills/spec_navigator/scripts/query_md_section.py /home/crrb/ag_projects/portal_da-tica/inst.md "Persistencia"
    ```
3.  The output will be just the ~10 lines related to persistence, saving ~150 lines of context.

## Common Search Terms for `inst.md`

*   `"Arquitectura"` - For stack and module layout.
*   `"MVP"` or `"Alcance"` - For the immediate to-do list.
*   `"Persistencia"` - For database and volume mounts.
*   `"Restricciones"` - For negative constraints (what NOT to do).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chris78rey) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
