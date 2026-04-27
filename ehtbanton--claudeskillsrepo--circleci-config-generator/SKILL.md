---
name: circleci-config-generator
description: Generate CircleCI configuration files with workflows, orbs, and deployment. Triggers on "create circleci config", "generate circleci configuration", "circleci pipeline", "circle ci setup". Use when this capability is needed.
metadata:
  author: ehtbanton
---

# CircleCI Config Generator

Generate CircleCI pipeline configuration files with workflows and orbs.

## Output Requirements

**File Output:** `.circleci/config.yml`
**Format:** Valid CircleCI YAML
**Standards:** CircleCI 2.1

## When Invoked

Immediately generate a complete CircleCI pipeline configuration.

## Example Invocations

**Prompt:** "Create CircleCI config for Node.js application"
**Output:** Complete `.circleci/config.yml` with test and deploy workflows.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ehtbanton) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
