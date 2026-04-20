---
name: supabase-codegen
description: Generate TypeScript types or signing keys. Triggered by phrases like "generate types", "supabase gen types", or "create signing key". Use when this capability is needed.
metadata:
  author: diatonic-ai
---

# Supabase Codegen Skill

## Goal
Automate code generation for type safety and security.

## Instructions
1.  Open the relevant rule file:
    - `supabase gen types` -> [.agent/rules/supabase/commands/gen/types.md](../../rules/supabase/commands/gen/types.md)
    - `supabase gen signing-key` -> [.agent/rules/supabase/commands/gen/signing-key.md](../../rules/supabase/commands/gen/signing-key.md)
2.  Use `gen types` after schema changes to maintain frontend consistency.
3.  Prefer `--linked` flag when generating types for a remote project.

## Examples
- "Generate TypeScript types" -> Use `supabase gen types typescript`
- "Create a new signing key" -> Use `supabase gen signing-key`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/diatonic-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
