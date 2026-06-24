---
name: ci-cd-guardrails
description: Skill for building safer, faster, and more reliable delivery pipelines Use when this capability is needed.
metadata:
  author: coozyme
---

# CI/CD Guardrails Skill

Guidelines for enforcing quality and safety gates in build, test, and deployment workflows.

## When to Use This Skill

- When creating or hardening CI/CD pipelines
- When pull requests often break main branch
- When releases are frequent and rollback risk is high
- When flaky tests block developer productivity

## Guardrail Layers

### 1. Pre-Merge Guardrails
- Require branch protection and at least one review
- Run mandatory checks: lint, unit tests, static analysis
- Require all checks to pass before merge
- Block force-push on protected branches

### 2. Build and Test Guardrails
- Use deterministic builds (lock files and pinned versions)
- Cache dependencies and build artifacts to reduce pipeline time
- Fail fast on formatting/lint errors before expensive steps
- Separate fast checks (PR) from full checks (main/nightly)

### 3. Deployment Guardrails
- Use progressive delivery (canary/rolling/blue-green)
- Add health-check gates before traffic switch
- Automate rollback when error budget or health checks fail
- Record deployment metadata (commit, actor, environment, timestamp)

## Flaky Test Handling Protocol

1. Detect flaky tests from historical failure patterns.
2. Quarantine flaky tests with a tracking ticket.
3. Keep quarantine list small and reviewed weekly.
4. Fix root cause quickly (timing, shared state, test data race).
5. Remove quarantine entry after stable runs.

## Pipeline Quality Checklist

- [ ] Branch protection is enabled
- [ ] Required status checks are configured
- [ ] Secrets are injected securely (not hardcoded)
- [ ] CI uses minimal permissions for tokens
- [ ] Pipeline runtime budget is defined and measured
- [ ] Deployment rollback is automated and tested
- [ ] Incident runbook is linked from pipeline docs

## Useful Commands

```bash
# Node.js
npm ci
npm run lint
npm test

# Python
pip install -r requirements.txt
ruff check .
pytest
```

## Output Expectations

When applying this skill, produce:
- Updated CI/CD workflow with explicit quality gates
- Rollback strategy and trigger conditions
- Short operational notes for on-call/deploy owners

---
> Source: [coozyme/agent-starter-kit](https://github.com/coozyme/agent-starter-kit) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
