---
name: start-wiring-config-generator
description: Use when generating start-module wiring configs (Bean configs/Scan configs) or extracting complex logic into dedicated testable classes.
metadata:
  author: ryan-alexander-zhang
---

# Start Wiring Config Generator

## Overview
Generates Spring `@Configuration` classes that wire ports to implementations, extracting complex policies into dedicated testable classes.
**REQUIRED:** Follow `GENERATOR_SKILL_STRUCTURE.md`. Variables in `VARIABLES.md`. Templates in `references/templates.md`.

## When to Use
- Generating or modifying wiring configs under `{{basePackage}}.start.config.bean.*`
- Extracting complex bean logic into testable policy classes

### Don't use when
- Adding or renaming YAML keys (use `start-yaml-config-generator`)
- Deciding YAML key naming rules (use `start-config-schema-guardrails`)

## Inputs Required
- Which port(s) to bind to which implementation(s)
- Required config keys + defaults
- Whether complex logic should be extracted to a dedicated class

## Outputs
- `.../start/config/bean/<XxxWiringConfig>.java`
- Extracted policy class(es) under `.../start/config/bean/...`
- Unit tests for extracted policy
- YAML updates under `{{startModuleDir}}/src/main/resources`

## Naming & Packaging
- Wiring configs belong in `start.config.bean`
- Keep feature-specific wiring separated (outbox/workflow/mq/etc.)

## Rules
- Keep `@Configuration` thin.
- Extract complex policies into dedicated classes (testable with unit tests).
- YAML keys must be aligned with `start-config-schema-guardrails`.

## Reference Implementations
- `{{startModuleDir}}/src/main/java/{{basePackagePath}}/start/config/bean/OutboxWiringConfig.java`
- `{{startModuleDir}}/src/main/java/{{basePackagePath}}/start/config/bean/WorkflowWiringConfig.java`

## Tests
- Prefer unit tests for extracted policy classes (pure Java, no Spring context needed).
- Minimum verification: `mvn -q clean test`.

## Common Mistakes
| Mistake | Why It Happens | Fix |
|---------|---------------|-----|
| Embedding 50+ lines of logic inside anonymous beans | Tempting to inline everything | Extract to dedicated policy class with unit tests |
| YAML keys not aligned with schema guardrails | Easy to forget cross-skill rules | Always check `start-config-schema-guardrails` |
| Mixing multiple feature wiring in one config class | Seems simpler to combine | Keep outbox/workflow/mq wiring separated |

## Integration
- **Called by:** `scaffold-router`
- **Pairs with:** `start-yaml-config-generator`, `start-config-schema-guardrails`, `adapter-scheduler-job-generator`

## Commit Gate

- pass required tests (`mvn -q clean test` minimum, `mvn -q clean verify` if DB behavior changed)
- run `requesting-code-review`
- resolve Critical/Important findings
- keep the commit focused

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ryan-alexander-zhang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
