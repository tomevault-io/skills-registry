---
name: supabase-management-apis
description: Manage Supabase organizations, projects, branches, domains, and custom configurations. Triggered by phrases like "list projects", "create organization", "manage branches", or "supabase orgs". Use when this capability is needed.
metadata:
  author: diatonic-ai
---

# Supabase Management APIs Skill

## Goal
Manage administrative aspects of Supabase entities (organizations, projects, etc.).

## Instructions
1.  Identify the entity being managed (org, project, branch, etc.).
2.  Open the relevant rule file:
    - `supabase orgs` -> [.agent/rules/supabase/commands/orgs.md](../../rules/supabase/commands/orgs.md)
    - `supabase projects` -> [.agent/rules/supabase/commands/projects.md](../../rules/supabase/commands/projects.md)
    - `supabase branches` -> [.agent/rules/supabase/commands/branches.md](../../rules/supabase/commands/branches.md)
    - `supabase domains` -> [.agent/rules/supabase/commands/domains.md](../../rules/supabase/commands/domains.md)
    - `supabase sso` -> [.agent/rules/supabase/commands/sso.md](../../rules/supabase/commands/sso.md)
3.  Ensure you are authenticated (`supabase login`) before running these commands.
4.  Check [00_global_policy.md](../../rules/supabase/00_global_policy.md) before deleting projects or branches.

## Examples
- "List all my organizations" -> Use `supabase orgs list`
- "Show all projects" -> Use `supabase projects list`
- "Create a new preview branch" -> Use `supabase branches create`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/diatonic-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
