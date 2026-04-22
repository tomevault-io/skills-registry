---
name: laravel-best-practices
description: Laravel development standards. Triggers when working with Laravel applications, Eloquent ORM, Blade templates, or Livewire components. Use when this capability is needed.
metadata:
  author: jcastillotx
---

# Laravel Best Practices

Comprehensive coding standards for Laravel applications, optimized for AI agents and LLMs.

## Overview

This skill provides 26 rules organized across 8 categories:

1. **Security (security-)** - CSRF, XSS, SQL injection, mass assignment [CRITICAL]
2. **Eloquent Optimization (eloquent-)** - Eager loading, chunking, lazy collections [CRITICAL]
3. **Performance (perf-)** - Route caching, config caching, OPcache [HIGH]
4. **Architecture (arch-)** - Service containers, repositories, actions [HIGH]
5. **API Development (api-)** - Resources, rate limiting, versioning [MEDIUM-HIGH]
6. **Testing (test-)** - Feature tests, mocking, factories [MEDIUM]
7. **Queue & Jobs (queue-)** - Job batching, rate limiting, retries [MEDIUM]
8. **Livewire (livewire-)** - Wire navigation, lazy loading, polling [LOW-MEDIUM]

## Usage

Reference this skill when:
- Building Laravel applications
- Optimizing Eloquent queries
- Implementing API endpoints
- Setting up job queues
- Writing tests

## Build

```bash
pnpm build    # Compile rules to AGENTS.md
pnpm validate # Validate rule files
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcastillotx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
