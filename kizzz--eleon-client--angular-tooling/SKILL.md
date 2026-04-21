---
name: angular-tooling
description: Angular 20+ tooling specialist for Nx-first client work. Use after the `angular` router when the task needs generators, target selection, workspace config, build/test/lint commands, or Angular/Nx setup decisions. Use when this capability is needed.
metadata:
  author: kizzz
---

# Angular Tooling

## When to use

- Running or changing Nx targets, generators, build config, test config, or lint config
- Choosing between `nx`, `ng`, and workspace-specific automation
- Adding or adjusting project wiring for new Angular frontend code
- Investigating why a frontend target, generator, or configuration is failing

## Do not use

- Pure component/UI design; use [`angular-ui-patterns`](../angular-ui-patterns/SKILL.md)
- Pure DI or state design; use the relevant specialist skill
- Runtime verification only; use [`frontend-verification`](../frontend-verification/SKILL.md)
- Dependency cleanup audits only; use [`dependency-analysis`](../../skills-optional/dependency-analysis/SKILL.md)

## Instructions

1. Prefer Nx-first commands in this pack:
   - generators before hand-created boilerplate
   - project targets before custom one-off scripts
   - affected runs when scope is known
2. Inspect existing project patterns before creating new config.
3. Keep build, lint, test, and serve changes as narrow as possible.
4. If Angular CLI is needed, use it only where it fits the Nx workspace instead of bypassing workspace conventions.
5. Validate touched targets after config changes.
6. Keep tool guidance neutral and current for Angular 20+ rather than future-version speculation.

## Default rules

- Reuse existing workspace generators and target naming
- Avoid duplicating config that the workspace already centralizes
- Prefer minimal config deltas over custom tooling branches
- Document any new target or generator expectations in adjacent docs when needed

## Related skills

- [`angular`](../angular/SKILL.md)
- [`angular-routing`](../angular-routing/SKILL.md)
- [`angular-di`](../angular-di/SKILL.md)
- [`angular-best-practices`](../angular-best-practices/SKILL.md)
- [`angular-testing`](../angular-testing/SKILL.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kizzz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
