---
name: supabase-policy-guardrails
description: | Use when this capability is needed.
metadata:
  author: helixdevelopment
---

# Supabase Policy Guardrails

## Prerequisites
- ESLint configured in project
- Pre-commit hooks infrastructure
- CI/CD pipeline with policy checks
- TypeScript for type enforcement

## Instructions

### Step 1: Create ESLint Rules
Implement custom lint rules for Supabase patterns.

### Step 2: Configure Pre-Commit Hooks
Set up hooks to catch issues before commit.

### Step 3: Add CI Policy Checks
Implement policy-as-code in CI pipeline.

### Step 4: Enable Runtime Guardrails
Add production safeguards for dangerous operations.

## Output
- ESLint plugin with Supabase rules
- Pre-commit hooks blocking secrets
- CI policy checks passing
- Runtime guardrails active

## Error Handling

See `{baseDir}/references/errors.md` for comprehensive error handling.

## Examples

See `{baseDir}/references/examples.md` for detailed examples.

## Resources
- [ESLint Plugin Development](https://eslint.org/docs/latest/extend/plugins)
- [Pre-commit Framework](https://pre-commit.com/)
- [Open Policy Agent](https://www.openpolicyagent.org/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/helixdevelopment) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
