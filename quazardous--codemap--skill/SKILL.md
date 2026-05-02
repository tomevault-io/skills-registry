---
name: codemap
description: Use codemap to find patterns, understand architecture, and write better code Use when this capability is needed.
metadata:
  author: quazardous
---

# Codemap Skill

**Purpose:** Semantic code index for architectural guidance and pattern discovery.

**Philosophy:** Codemap is a compass, not GPS. It guides you to the right code, then you read and understand it.

**Note:** Indexing and enrichment are done via CLI (`codemap index`, `codemap auto-enrich`), not inside Claude.

---

## For User: Setup Project

### 1. Ensure Coding Agents Know About Codemap

Check if `CODEMAP_CODING_AGENT.md` exists at project root:
```
Read: CODEMAP_CODING_AGENT.md
```

This file teaches coding agents when/how to use codemap vs Grep/Glob.

**If CLAUDE.md exists**, suggest adding:
```markdown
## Codemap Integration
See CODEMAP_CODING_AGENT.md for usage guide.
Key tools: codemap_query, codemap_get_symbol, codemap_search
```

### 2. When Spawning Coding Agents

Include in agent prompt:
```
"Read CODEMAP_CODING_AGENT.md for codemap usage guidance."
```

---

## For Agent: When to Use Codemap

### Use codemap when:
- **User wants architectural overview** → Use `/cm:status`
- **User searches for patterns/symbols** → Use `/cm:query`
- **Coding agents need pattern guidance** → They should read `CODEMAP_CODING_AGENT.md`

### Don't use codemap when:
- User wants to read/edit specific files (use Read/Edit)
- User wants to search exact strings (use Grep)
- User wants to list files (use Glob)

**Delegation:** For coding tasks, spawn agents with access to `CODEMAP_CODING_AGENT.md`.

---

## Available Commands

| Command | When to Use |
|---------|-------------|
| `/cm:status` | User wants index overview (coverage, stats) |
| `/cm:query` | User wants to search for specific symbols/patterns |

---

## Core Concepts (Brief)

**Package-centric indexing:**
- **Packages** (classes, interfaces, modules) are the primary units
- **Methods/functions** are secondary (APIs of packages)

**Enrichment fields:**
- `domain` - Business domain (auth, payment, etc.)
- `layer` - Architecture layer (controller, service, repository)
- `usage.score` - Pattern (+) or anti-pattern (-)
- `flags` - Warnings (deprecated, security, todo)
- `quality` - Complexity, coupling, testability

**Signal reliability:**
- `mechanical.*` (calls, called_by) = ground truth
- `ai.*` (enrichment) = heuristic, check confidence
- `status: "stale"` = enrichment may be outdated

**For full guide:** See `CODEMAP_CODING_AGENT.md`.

---

## Quick Reference: MCP Tools

| MCP Tool | Purpose |
|----------|---------|
| `codemap_query` | Unified search: FTS + filters (domain, layer, flags, regex, etc.) |
| `codemap_get_symbol` | Get basic symbol details (enrichment, source) |
| `codemap_describe` | Get comprehensive details (hints, members, inheritance, deps) |
| `codemap_status` | Get index status and health information |
| `codemap_report` | Generate comprehensive report (coverage, flags, domains/tags) |
| `codemap_get_package_dependencies` | Get packages that a symbol imports |
| `codemap_get_package_dependents` | Get packages that import a symbol (reverse deps) |
| `codemap_get_most_referenced_packages` | Find core packages by reference count |

### `codemap_query` Search Capabilities

**Full-text search (fts):**
```
"user auth"         → OR search (prefix match)
"user AND auth"     → both required
'"user service"'    → exact phrase
"user NOT admin"    → exclude term
```

**Pattern matching:**
| Param | Type | Example |
|-------|------|---------|
| `name` | LIKE | `"Service"` (substring, case-insensitive) |
| `name_glob` | GLOB | `"*Manager"` (case-sensitive, `*`, `?`, `[...]`) |
| `name_regex` | REGEXP | `"^Abstract"` (full regex) |
| `path_regex` | REGEXP | `"src/.*test"` (path regex) |

**Combined example:**
```json
{"fts": "authentication", "name_regex": "^.*Service$", "layer": "service"}
```

**For full guide:** See `CODEMAP_CODING_AGENT.md`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/quazardous) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
