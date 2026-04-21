---
name: typora-markdown
description: Open markdown content in Typora for enhanced viewing. Use when creating plans, PR reviews, analysis reports, or substantial markdown content. Triggers on: plan files, /review-pr output, codebase analysis, documentation generation, or explicit user request to view in Typora. Use when this capability is needed.
metadata:
  author: petestewart
---

# Typora Markdown Viewer

Open markdown in Typora for rich viewing/editing.

## Usage

### Existing files (user-specified path like PRD.md)
```bash
./scripts/open-in-typora.sh /path/to/file.md
```

### Ephemeral content (PR reviews, analysis)
Save to `~/.claude/typora/<context>-<YYYYMMDD-HHMMSS>.md`:

```bash
cat <<'EOF' | ./scripts/open-in-typora.sh ~/.claude/typora/pr-review-4447-20260106-143022.md --stdin
# PR Review: Feature Name
...content...
EOF
```

## Suppression
Skip if user says: "don't open in Typora", "skip Typora", "no Typora", "terminal only"

## File naming examples
- `pr-review-4447-20260106-143022.md`
- `codebase-analysis-20260106-143022.md`
- `plan-feature-name-20260106-143022.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/petestewart) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
