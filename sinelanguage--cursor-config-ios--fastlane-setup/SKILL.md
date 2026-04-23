---
name: fastlane-setup
description: Set up Fastlane lanes for build, test, and TestFlight. Use when this capability is needed.
metadata:
  author: sinelanguage
---
# Fastlane Setup

Configure Fastlane for build, test, and TestFlight upload.

## When to Use

- When you need CI/CD or TestFlight automation.

## Inputs

- App scheme and bundle ID
- Signing strategy (automatic or match)

## Instructions

1. Create `fastlane/Fastfile` and `fastlane/Appfile`.
2. Add lanes for `lint`, `tests`, and `beta`.
3. Document required secrets for CI.

## Output

- Fastlane configuration and setup notes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sinelanguage) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
