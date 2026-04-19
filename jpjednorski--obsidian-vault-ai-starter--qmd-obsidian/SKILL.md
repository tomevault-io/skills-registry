---
name: qmd-obsidian
description: Guides use of qmd to index and search this Obsidian vault. Use when the user mentions qmd/QMD, searching notes/docs/meetings, or Obsidian vault search. Use when this capability is needed.
metadata:
  author: jpjednorski
---

# QMD for Obsidian Vault

## Quick start

1. Check index status:
   - `qmd status`
2. If the vault is not a collection, add it:
   - `qmd collection add "<vault-root>" --name obsidian-vault --mask "**/*.md"`
3. Add context for better results:
   - `qmd context add qmd://obsidian-vault "Primary Obsidian vault with notes, meetings, projects, people"`

## Search workflow

1. Start with BM25 keyword search:
   - `qmd search "exact phrase or term" -c obsidian-vault`
2. If results are thin, use semantic search:
   - `qmd vsearch "natural language question" -c obsidian-vault`
3. For best quality, use hybrid with reranking:
   - `qmd query "topic or question" -c obsidian-vault`

## Retrieving content

- Get a single note by path or docid:
  - `qmd get "Meetings/20260126 General Onboarding Call.md"`
  - `qmd get "#abc123"`
- Get multiple notes by glob:
  - `qmd multi-get "Meetings/*.md"`

## Output formats

- Use `--json` for structured results
- Use `--files` to return file paths only
- Use `--md` for markdown-ready output
- Pick format based on task requirements

## Troubleshooting

- If a note is missing, re-index:
  - `qmd update`
- If collection name differs, check:
  - `qmd collection list`

## Examples

- Find a specific topic across notes:
  - `qmd search "onboarding plan" -c obsidian-vault`
- Ask a broader question:
  - `qmd query "what was discussed about onboarding timelines?" -c obsidian-vault`
- List all matching files above a threshold:
  - `qmd query "project status" --all --files --min-score 0.3 -c obsidian-vault`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jpjednorski) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
