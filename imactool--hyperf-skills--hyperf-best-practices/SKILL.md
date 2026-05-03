---
name: hyperf-best-practices
description: Hyperf framework best practices for high-performance coroutine-based PHP applications. Covers Dependency Injection, Coroutine safety, and Performance tuning. Use when this capability is needed.
metadata:
  author: imactool
---

## Capability Rules

| Rule | Keywords | Description |
|------|----------|-------------|
| [no-global-variables](rules/no-global-variables.md) | global, request, singleton | Avoid using global variables for request data |
| [di-singleton-state-safety](rules/di-singleton-state-safety.md) | di, singleton, state, race condition | Ensure DI singletons are stateless |
| [avoid-magic-methods-coroutine-switch](rules/avoid-magic-methods-coroutine-switch.md) | magic method, coroutine, deadlock | Avoid I/O in magic methods |

## Efficiency Rules

| Rule | Keywords | Description |
|------|----------|-------------|
| [blocking-code-in-coroutine](rules/blocking-code-in-coroutine.md) | blocking, sleep, io, performance | Avoid blocking code in coroutines |
| [production-deployment-scan](rules/production-deployment-scan.md) | deployment, optimization, scan | Optimize autoloader and scan cache |

## Reference

- [Hyperf Documentation](https://hyperf.wiki/)
- [Swoole Documentation](https://wiki.swoole.com/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/imactool) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
