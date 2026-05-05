---
name: findall
description: | Use when this capability is needed.
metadata:
  author: neversight
---

# FindAll CLI

Discover companies, people, or entities matching complex criteria using natural language.

## Installation

```bash
npm install -g findall-cli
```

## Usage

```bash
findall run <output-dir> "<objective>"
```

**Options:**
| Option | Description | Default |
|--------|-------------|---------|
| `-k, --api-key <key>` | API key (or set `PARALLEL_API_KEY`) | - |
| `-g, --generator <tier>` | base, core, pro, preview | `core` |
| `-l, --limit <number>` | Max matches (5-1000) | `50` |
| `--skip-preview` | Skip schema editor | `false` |
| `--auto-approve` | Non-interactive mode | `false` |

## Examples

```bash
# Find AI startups with recent funding
findall run ./leads "Find AI companies that raised Series A in 2024"

# Find security-compliant vendors
findall run ./vendors "Find SaaS companies with SOC2 Type II certification"

# Find hiring companies
findall run ./prospects "Find fintech startups with 50-200 employees hiring engineers"

# Non-interactive for scripts
findall run ./results "Find e-commerce platforms in Europe" --skip-preview --auto-approve
```

## Generator Tiers

- `base` - Fast, simple criteria
- `core` - Balanced (recommended)
- `pro` - Complex criteria, higher accuracy
- `preview` - Experimental

## Output

Results saved to output directory:
- `matches.json` - Matched entities with details and citations
- `matches.csv` - CSV export for spreadsheets
- `summary.json` - Run statistics
- `candidates.json` - All evaluated entities

## Tips

**Be specific in objectives:**
- Bad: "Find tech companies"
- Good: "Find B2B SaaS companies with SOC2 certification that raised Series B or later"

**Use higher limits for broad searches:**
```bash
findall run ./results "..." --limit 200 --generator pro
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
