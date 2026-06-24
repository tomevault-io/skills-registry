---
name: dependency-tracker
description: > Use when this capability is needed.
metadata:
  author: blessonism
---

# Dependency Tracker

Track version drift across all OpenClaw dependencies. Detect updates, generate reports, notify Boss.

## Quick Start

```bash
# 1. Scan — build/refresh the local manifest
python3 scripts/scan.py

# 2. Check — compare local vs remote versions
python3 scripts/check.py

# 3. Report — generate human-readable Markdown report
python3 scripts/report.py
```

All scripts are in the skill directory: `~/.openclaw/workspace/skills/dependency-tracker/scripts/`

## What It Tracks

| Category | Source | Detection Method |
|----------|--------|-----------------|
| Managed skills (GitHub) | `.skill-lock.json` | GitHub Contents API (git hash compare) |
| Managed skills (ClewHub) | `_meta.json` | ClewHub API (version compare) |
| Bundled skills | `/app/skills/` | Tied to OpenClaw version |
| Workspace skills | `workspace/skills/` | Local git (self-managed) |
| OpenClaw version | npm registry + GitHub | `npm view` / GitHub Releases API |
| npm dependencies | `/app/node_modules/` | `npm outdated` |
| pip packages | system Python | `pip3 list --outdated` |
| CLI tools | PATH | Version commands |

## Workflow

### On-Demand Check

When user asks to check dependencies:

1. Run `scan.py` to refresh the manifest
2. Run `check.py` to compare against remote sources
3. Run `report.py` to generate the report
4. Send report summary to user (Telegram)
5. If unknown-source skills found, notify Boss

### Scheduled Check (Cron)

Set up a weekly cron job:
- Run all three scripts in sequence
- Push report summary to Telegram
- Only notify if updates are found or errors occur

## Data Files

All runtime data lives in `data/` (gitignored from backup):

```
data/
├── manifest.json        # Full dependency inventory
├── check-results.json   # Latest check results
└── reports/
    └── YYYY-MM-DD-report.md  # Generated reports
```

## Key Design Decisions

- **No auto-update**: Only detect and report. User decides when to update.
- **GitHub hash comparison**: Uses `git hash-object` locally vs GitHub Contents API SHA — exact match, no false positives.
- **Lock file as source of truth**: `.skill-lock.json` (OpenClaw's managed skill registry) provides repo URLs and install metadata.
- **Changelog extraction**: For GitHub skills with updates, fetches recent commits for context.
- **Unknown source notification**: Skills without traceable source are flagged and reported to Boss.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/blessonism) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
