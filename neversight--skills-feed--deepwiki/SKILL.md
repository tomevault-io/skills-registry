---
name: deepwiki
description: > Use when this capability is needed.
metadata:
  author: neversight
---

# deepwiki

Query any public GitHub repo's docs from the terminal via DeepWiki. Assumes Node.js is installed.

## Commands

| Command | Usage | Description |
|---------|-------|-------------|
| `toc`   | `npx @seflless/deepwiki toc <owner/repo>` | Table of contents |
| `wiki`  | `npx @seflless/deepwiki wiki <owner/repo>` | Full wiki content |
| `ask`   | `npx @seflless/deepwiki ask <owner/repo> "<question>"` | AI-powered Q&A |
| `ask`   | `npx @seflless/deepwiki ask <repo1> <repo2> "<question>"` | Multi-repo Q&A (max 10) |

## Flags

| Flag | Purpose |
|------|---------|
| `--json` | Raw JSON output (good for piping) |
| `-q, --quiet` | No spinners/progress |
| `--no-color` | Disable colors |

## Examples

```bash
# Understand a library's structure
npx @seflless/deepwiki toc facebook/react

# Get full docs for reference
npx @seflless/deepwiki wiki oven-sh/bun --json > bun-docs.json

# Ask a specific question
npx @seflless/deepwiki ask anthropics/claude-code "How does the tool permission system work?"

# Cross-project question
npx @seflless/deepwiki ask facebook/react vercel/next.js "How do server components work across these projects?"
```

## Tips

- Use `--json` when you need structured data to parse
- Use `toc` first to understand what docs exist, then `ask` for specifics
- Multi-repo `ask` is great for understanding how libraries interact

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
