---
name: chippery-orient
description: Discover files related to a concept. Use when exploring unfamiliar code or finding where a feature is implemented. Use when this capability is needed.
metadata:
  author: paircat
---

# Codebase Navigator - /chippery-orient

**Use this when you don't know where to look.** Instead of grepping blindly or reading random files, orient gives you a ranked list of relevant files based on semantic understanding.

## Usage

```bash
MODE=$(cat ~/.chippery/mode 2>/dev/null || echo "balanced")
CONFIG=$(~/.chippery/bin/chippery-license config "$MODE" 2>/dev/null)
FILE_LIMIT=$(echo "$CONFIG" | grep -o '"ownershipOutputFiles":[0-9]*' | grep -o '[0-9]*' || echo "5")
~/.chippery/bin/chippery-navigator query "$(pwd)" "$ARGUMENTS" --mode "$MODE" | jq --argjson limit "$FILE_LIMIT" '{query: .query, concept: .concept, files: ((.ranked // [])[:$limit] | map({path: .path, reason: .reason})), fileCount: ((.ranked // []) | length)} | del(.[] | nulls)'
```

## When to Use Orient

| Situation | Instead of... | Use |
|-----------|---------------|-----|
| "Where is auth handled?" | `grep -r "auth"` (hundreds of matches) | `/chippery-orient auth` |
| "Find the payment flow" | Reading random files hoping to find it | `/chippery-orient payments` |
| "How does caching work?" | `grep "cache"` and wading through noise | `/chippery-orient caching` |
| "Where are API routes?" | Guessing directory names | `/chippery-orient "api routes"` |
| New to a codebase | Reading files at random | `/chippery-orient "entry points"` |

## Examples

```bash
# Find authentication/authorization code
/chippery-orient auth

# Find database models and queries
/chippery-orient "database models"

# Find API endpoint definitions
/chippery-orient "api routes"

# Rich queries - combine multiple concepts
/chippery-orient "oauth shopify etsy"        # OAuth for specific integrations
/chippery-orient "webhook stripe payment"    # Stripe webhook handlers
/chippery-orient "sync inventory amazon"     # Amazon inventory sync
```

## Rich Queries

Orient supports multi-term queries to narrow your search:

```bash
# Find specific integration points:
/chippery-orient "oauth shopify etsy"
/chippery-orient "api marketplace ebay"
/chippery-orient "webhook notification slack"
```

This finds files where these concepts intersect, not just files containing any single term.

## What It Returns

A compact JSON with:
- **query** - Your search query
- **concept** - Detected concept category (auth, database, api, etc.)
- **files** - Top ranked files with WHY each is relevant
- **fileCount** - Total matches found
- **relatedFiles** - Secondary files via import graph (if any)

Output is automatically limited based on your token mode (balanced=5 files, balanced-pro=4, frugal=3).

## Workflow: Orient → Func

1. **Orient first** - `/chippery-orient auth` → finds AuthController.php
2. **Then func** - `/chippery-func AuthController.login` → Get source + callers + callees

This is faster and uses fewer tokens than reading multiple files.

## Tips

- Use natural language: "user signup flow", "payment processing"
- Start broad, then narrow: `/chippery-orient auth` → `/chippery-orient "oauth flow"`
- Combine with func: Orient finds the files, func dives into specific methods

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/paircat) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
