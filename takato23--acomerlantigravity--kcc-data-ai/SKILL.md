---
name: kcc-data-ai
description: Use for Supabase schema, RLS policies, API design, and AI integration settings. Use when this capability is needed.
metadata:
  author: takato23
---

# Data and AI Integration Guide

## Quick start
- Database schema: docs/DATABASE_SCHEMA.md
- API design: docs/API_DESIGN.md and docs/API_DOCUMENTATION.md
- Supabase setup: SUPABASE_SETUP.md and supabase/ folder
- RLS notes: SUPABASE_RLS_FIX.md and SUPABASE_RLS_SIMPLE.sql
- AI integration: docs/AI_INTEGRATION.md and GEMINI_SETUP.md

## Workflow
1. Review schema and API docs before changing types or tables.
2. If editing RLS or SQL, check supabase/ and root *.sql files.
3. Keep API routes aligned with docs/API_DESIGN.md.
4. Update tests or fixtures if schema changes affect data.

## Useful paths
- supabase/
- src/lib/supabase
- src/lib/ai
- src/services/gemini
- src/services/ai

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/takato23) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
