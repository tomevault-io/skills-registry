---
name: python-architecture
description: > Use when this capability is needed.
metadata:
  author: microsoft
---

# Python Architecture Skill

[Python architect persona](../../agents/python-architect.agent.md)

## When to activate

- Creating new Python modules or packages under `src/apm_cli/`
- Refactoring class hierarchies or introducing base classes
- Changes that touch 3+ files with shared logic patterns
- Introducing new design patterns (Strategy, Observer, etc.)
- Cross-cutting concerns (logging, auth, error handling)
- Performance-sensitive paths (parallel downloads, large manifests)

## Key rules

- Follow existing patterns (BaseIntegrator, CommandLogger, AuthResolver) before inventing new ones
- Prefer composition over deep inheritance
- Push shared logic into base classes, not duplicated across siblings

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/microsoft) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
