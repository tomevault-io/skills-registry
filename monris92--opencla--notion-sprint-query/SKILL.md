---
name: notion-sprint-query
description: Query Notion Dev Tickets database by sprint number Use when this capability is needed.
metadata:
  author: monris92
---

# notion-sprint-query

Query the Notion Dev Tickets database filtered by sprint number.

## Usage

When user asks to review sprint tickets (e.g., "review tiket sprint 2", "list sprint 1 tickets"), use this command:

```bash
python3 ~/moltbot-workspace/scripts/get-tickets-sprint.py <sprint_number>
```

## Examples

**Sprint 2 tickets:**
```bash
python3 ~/moltbot-workspace/scripts/get-tickets-sprint.py 2
```

**Sprint 1 tickets:**
```bash
python3 ~/moltbot-workspace/scripts/get-tickets-sprint.py 1
```

## Output Format

The script returns JSON with complete ticket details:
- Title (Name property)
- Status
- Assignee
- Sprint relation
- All other database properties

## Technical Details

- Uses Notion API v2022-06-28 (legacy endpoint for old databases)
- Automatically handles UUID format (removes dashes for relation filters)
- Implements pagination (handles 2000+ tickets)
- Credentials from: `~/moltbot-workspace/.env`
- Dependencies: `httpx`, `python-dotenv` (see `scripts/requirements.txt`)

## Database IDs

Database IDs are loaded from environment variables:
- `DATABASE_DEV` → Dev Tickets database
- `SPRINT_DATABASE_ID` → Sprint Timeline database

Credentials: `~/moltbot-workspace/.env`

## When to Use

Use this skill when:
- User asks "review tiket sprint X"
- User asks "list sprint tickets"
- User wants to see what's in a specific sprint
- User asks about sprint progress/status

**IMPORTANT:** Always use this script (`get-tickets-sprint.py`) for sprint queries. Do NOT use other scripts like `sprint2-deden.py`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/monris92) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
