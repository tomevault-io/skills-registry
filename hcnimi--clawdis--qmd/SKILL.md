---
name: qmd
description: Local search/indexing CLI (BM25 + vectors + rerank) with MCP mode. Use when this capability is needed.
metadata:
  author: hcnimi
---

# qmd

Use `qmd` to index local files and search them.

Indexing
- Add collection: `qmd collection add /path --name docs --mask "**/*.md"`
- Update index: `qmd update`
- Status: `qmd status`

Search
- BM25: `qmd search "query"`
- Vector: `qmd vsearch "query"`
- Hybrid: `qmd query "query"`
- Get doc: `qmd get docs/path.md:10 -l 40`

Notes
- Embeddings/rerank use Ollama at `OLLAMA_URL` (default `http://localhost:11434`).
- Index lives under `~/.cache/qmd` by default.
- MCP mode: `qmd mcp`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hcnimi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
