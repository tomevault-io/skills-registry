---
name: merge-resolve
description: AI-powered git merge conflict resolution Use when this capability is needed.
metadata:
  author: thinkfleetai
---

# Merge Resolver

Got merge conflicts? This tool understands both versions and picks the right resolution.

## Quick Start

```bash
npx ai-merge-resolve
```

## What It Does

- Finds all merge conflicts in repo
- Analyzes both versions semantically
- Suggests intelligent resolutions
- Can auto-resolve simple conflicts

## Usage Examples

```bash
# Resolve all conflicts
npx ai-merge-resolve

# Resolve specific file
npx ai-merge-resolve ./src/api.ts

# Auto-resolve obvious ones
npx ai-merge-resolve --auto

# Interactive mode
npx ai-merge-resolve --interactive
```

## How It Works

Doesn't just pick "theirs" or "ours". Actually reads the code, understands intent, and merges the functionality properly.

## Output

```
Resolving src/utils.ts...
- Conflict 1: Both added logging → Combined both log statements
- Conflict 2: Different error messages → Kept more descriptive one
✓ Resolved 2 conflicts
```

## Requirements

Node.js 18+. OPENAI_API_KEY required. Must have active merge conflicts.

## License

MIT. Free forever.

---

**Built by LXGIC Studios**

- GitHub: [github.com/lxgicstudios/ai-merge-resolve](https://github.com/lxgicstudios/ai-merge-resolve)
- Twitter: [@lxgicstudios](https://x.com/lxgicstudios)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thinkfleetai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
