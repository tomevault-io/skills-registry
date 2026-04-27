---
name: gitlab-ci-config-generator
description: Generate GitLab CI/CD pipeline configuration files with stages, jobs, and deployment. Triggers on "create gitlab ci", "generate gitlab-ci.yml", "gitlab pipeline", "gitlab ci config". Use when this capability is needed.
metadata:
  author: ehtbanton
---

# GitLab CI Config Generator

Generate GitLab CI/CD pipeline configuration files.

## Output Requirements

**File Output:** `.gitlab-ci.yml`
**Format:** Valid GitLab CI YAML
**Standards:** GitLab CI latest

## When Invoked

Immediately generate a complete GitLab CI pipeline configuration.

## Example Invocations

**Prompt:** "Create GitLab CI for Node.js with Docker deploy"
**Output:** Complete `.gitlab-ci.yml` with test, build, and deploy stages.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ehtbanton) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
