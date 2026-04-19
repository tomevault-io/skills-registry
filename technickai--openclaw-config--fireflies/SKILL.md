---
name: fireflies
description: Query meeting transcripts — AI notetaker for Zoom, Google Meet, Teams. Use when this capability is needed.
metadata:
  author: technickai
---

# Fireflies.ai 🔥

Query meeting transcripts — AI notetaker for Zoom, Google Meet, Teams.

## Setup

API key from app.fireflies.ai → Integrations → Fireflies API. Configure via gateway.

## What Users Ask

- "What meetings did I have today?"
- "What was discussed in the [project] meeting?"
- "Find meetings about [topic]"
- "What were the action items from yesterday's call?"
- "Get the transcript from my call with [person]"

## Capabilities

- Recent transcripts
- Search across all meetings
- Filter by date
- Full transcript retrieval by ID

## Response Data

**List view:**

- `id` — Transcript ID
- `title` — Meeting title from calendar
- `duration` — Length in minutes
- `participants` — Attendee emails
- `summary` — AI-generated overview and action items

**Full transcript:**

- Everything above plus full text with speaker names and timestamps

## Notes

- Speaker names come from calendar invites
- Works with Zoom, Google Meet, Microsoft Teams

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/technickai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
