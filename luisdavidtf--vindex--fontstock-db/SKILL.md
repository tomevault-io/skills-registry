---
name: fontstock-db
description: > Use when this capability is needed.
metadata:
  author: luisdavidtf
---

## Core Principles
1. **Normalization**: 3NF where possible, but pragmatic for mobile performance.
2. **Offline-First**: Schema must support synchronization indicators (dirty flags, last_updated).

## CRITICAL RULES

### 1. Table Names
- **ALWAYS** use singular nouns for table names in code variables (e.g., `productTable`) but plural in DB if Drizzle default (or consistent with team pref). *Correction: Drizzle often prefers single, but let's stick to what's defined in schema.ts. If unsure, check existing.*

### 2. Common Fields
- **ALWAYS** include `created_at` and `updated_at`.
- **ALWAYS** include `deleted_at` if soft delete is required.

### 3. Foreign Keys
- **ALWAYS** index foreign keys for query performance.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luisdavidtf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
