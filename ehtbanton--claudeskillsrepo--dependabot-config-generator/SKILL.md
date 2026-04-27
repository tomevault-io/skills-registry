---
name: dependabot-config-generator
description: Generate Dependabot configuration for GitHub automated dependency updates. Triggers on "create dependabot config", "generate dependabot configuration", "dependabot setup", "github dependency updates". Use when this capability is needed.
metadata:
  author: ehtbanton
---

# Dependabot Config Generator

Generate GitHub Dependabot configuration for automated dependency updates.

## Output Requirements

**File Output:** `.github/dependabot.yml`
**Format:** Valid Dependabot YAML configuration
**Standards:** GitHub Dependabot v2

## When Invoked

Immediately generate a complete Dependabot configuration for the project.

## Example Invocations

**Prompt:** "Create dependabot config for npm and GitHub Actions"
**Output:** Complete `.github/dependabot.yml` with npm and actions updates.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ehtbanton) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
