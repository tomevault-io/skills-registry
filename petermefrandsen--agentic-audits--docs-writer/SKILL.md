---
name: docs-writer
description: > Use when this capability is needed.
metadata:
  author: petermefrandsen
---

# Documentation Writer

## When to use

Use this skill when your mission involves creating new documentation or
updating existing docs — including READMEs, guides, API references, or
any markdown files.

## Instructions

### Writing new documentation

1. **Understand the audience** — determine if the doc targets end users,
   developers, or operators.

2. **Use a consistent structure**:
   ```markdown
   # Title

   > One-line description of what this document covers.

   ## Overview
   Brief context and purpose.

   ## Prerequisites
   What the reader needs before starting.

   ## Sections
   Organized by topic, task, or concept.

   ## Examples
   Concrete, runnable examples where applicable.

   ## Troubleshooting
   Common issues and solutions.

   ## References
   Links to related documentation.
   ```

3. **Add a table of contents** for documents longer than 3 sections.

4. **Use code blocks** with language hints for all code snippets.

### Updating existing documentation

1. **Preserve tone and style** — match the existing document's voice.
2. **Mark what changed** — add a changelog entry or note if the doc has one.
3. **Preserve internal notes** — do not remove comments marked with
   `<!-- internal -->` or similar annotations.
4. **Validate links** — ensure all cross-references and URLs are still valid.

## Rules

- Write in clear, concise English.
- Use active voice.
- One sentence per line in source markdown (for clean diffs).
- Never remove content without explanation.
- If unsure about factual accuracy, flag it with `<!-- TODO: verify -->`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/petermefrandsen) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
