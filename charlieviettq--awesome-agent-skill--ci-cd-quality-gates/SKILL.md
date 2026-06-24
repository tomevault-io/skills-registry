---
name: ci-cd-quality-gates
description: > Use when this capability is needed.
metadata:
  author: charlieviettq
---

# CI/CD quality gates

## Principles

- Fast feedback on every PR; expensive jobs scheduled or nightly
- Fail closed on security and contract breaks
- Same commands locally and in CI

## Tiered gates

| Tier | When | Examples |
|------|------|----------|
| PR required | Every push | Lint, unit tests, typecheck |
| PR optional | Large repos | Integration, e2e subset |
| Main/nightly | Post-merge | Full e2e, perf budget, dependency audit |

## Gate design

1. **Lint/format** — consistent style, catch syntax issues
2. **Unit tests** — required; flaky tests fixed or quarantined
3. **Build** — artifact or package builds for deployable repos
4. **Security** — secret scan, dependency audit (fail on critical)
5. **Contract** — OpenAPI/schema diff when APIs change

## Merge policy

- Required checks green before merge
- No force-push to protected default branch without policy
- Document bypass process for emergencies

## Anti-patterns

- 30+ minute PR feedback loops
- Different test command in CI vs README
- Ignored flaky tests accumulating

## Related

`verify-before-done`, `gstack/ship`, `test-failure-triage`, `api-security-testing`

---
> Source: [charlieviettq/awesome-agent-skill](https://github.com/charlieviettq/awesome-agent-skill) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
