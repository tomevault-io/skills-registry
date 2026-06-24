---
name: posting-linkedin
description: Use when publishing LinkedIn posts, configuring posting schedule,
metadata:
  author: abdullahmalik17
---
---
name: posting-linkedin
description: |
  Post content to LinkedIn using Playwright browser automation.
  Use when publishing LinkedIn posts, configuring posting schedule,
  troubleshooting LinkedIn authentication, or managing post queue.
  NOT when generating post content (use generating-ceo-briefing for metrics).
---

# LinkedIn Poster Skill

Browser automation for LinkedIn posting.

## Quick Start

```bash
# Process approved posts
python scripts/run.py --process-approved

# Post directly
python scripts/run.py --post "Your post content here"

# Check session
python scripts/run.py --check-session
```

## First Run

1. Browser opens automatically
2. Log in to LinkedIn manually
3. Session persists in `config/linkedin_data/`

## Rate Limits

- 2 posts per day
- 1 post per hour

## Approval Workflow

Posts go through `Vault/Pending_Approval/` before publishing.

## Verification

Run: `python scripts/verify.py`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/abdullahmalik17) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
