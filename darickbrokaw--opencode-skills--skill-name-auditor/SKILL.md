---
name: skill-name-auditor
description: Audits existing OpenCode skills for ASIS naming compliance, reports violations, and suggests compliant renames. Use when this capability is needed.
metadata:
  author: darickbrokaw
---

# Skill Name Auditor (ASIS Auditor)

## What I do
I scan your `skills/` directory to verify if existing skills comply with the **ASIS v2.0 Naming Standard**.

1. **Check Directory Name**: Must match OpenCode kebab-case format.
2. **Check Skill Name**: Must be `PascalCase_With_Underscores`.
3. **Run Compliance Test**: Checks Namespace, Action, Target, and Syntax.
4. **Suggest Renames**: Generates valid names for non-compliant skills.

## Usage
```bash
skill skill-name-auditor
```

## Output
Prints a JSON report of all skills:
- **PASS**: Skill is compliant.
- **FAIL**: Skill violates standard (with error codes and suggestions).

## Dependencies
- Requires `skill-name-creator` to be present for full suggestion logic.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/darickbrokaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
