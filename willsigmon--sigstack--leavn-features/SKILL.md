---
name: leavn-features
description: Leavn app features - Bible, search, reading plans, sermons, community, guided mode Use when this capability is needed.
metadata:
  author: willsigmon
---

# Leavn Features

Feature expertise for Leavn Bible app.

## Bible
- Chapter rendering with verse highlighting
- Translation switching (ESV, NIV, KJV, etc.)
- AI transformations (simplify, kids mode)
- Scripture database (SQLite FTS5)
- Reading modes (continuous, paginated)

## Search
- `UnifiedSearchService` - central search
- FTS5 full-text search on Scripture
- 50ms debounce for live search
- History and suggestions

## Reading Plans
- Plan progress tracking
- Streak calculations
- Day completion state
- Widget integration
- Plan catalog/discovery

## Sermons
- Recording with transcription
- AI analysis/summarization
- Playback with highlights
- Church directory integration
- Import from external sources

## Community
- Prayer wall (public/private)
- Groups with invites
- Messaging/comments
- CloudKit sync
- Content moderation

## Guided Mode
- Audio orchestration (TTS + ambient)
- Phase management (intro → scripture → reflection)
- Caption synchronization
- Scripture meditation flows

Use when: Feature bugs, understanding architecture, adding functionality

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willsigmon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
