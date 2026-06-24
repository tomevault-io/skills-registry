---
name: kubit-researching-prior-art
description: Use when designing new subsystem, comparing implementation approaches, or user asks how other frameworks handle something. Use when researching Laravel, Rails, AdonisJS, Inertia.js, or similar framework patterns.
metadata:
  author: aniftyco
---

# Researching Prior Art for Kubit

## Overview

When designing subsystems, research how established frameworks solve the same problem. Capture findings in docs/BRAINDUMP.md or the relevant RFC.

## When to Use

- Designing new subsystem (Auth, Validation, etc.)
- User asks "how does Laravel/Rails do X?"
- Comparing implementation approaches
- Need to justify design decisions

## Frameworks to Reference

| Framework | Strong Areas |
|-----------|--------------|
| **Laravel** | Eloquent ORM, Queue, Mail, Auth, Blade |
| **AdonisJS** | TypeScript patterns, decorators, Lucid ORM |
| **Rails** | ActiveRecord, conventions, generators |
| **Inertia.js** | SPA protocol, React SSR, forms |
| **Remix/Next** | React SSR, data loading, routing |

## Research Questions

When studying a pattern:
1. What's the public API? (method signatures, config)
2. What conventions are enforced?
3. What's the mental model? (how users think about it)
4. What are the extension points?
5. What do users complain about? (learn from mistakes)

## Capture Location

| Scope | Where to capture |
|-------|------------------|
| General philosophy | docs/BRAINDUMP.md |
| Specific subsystem research | Relevant RFC |
| Design decision | SPEC.md Decision Log |

## Research Template

```markdown
## [Subsystem] Prior Art

### Laravel
- API: `Mail::to($user)->send(new OrderShipped($order))`
- Conventions: Mailables as classes, queued by default
- Extension: Custom transports via driver config

### AdonisJS
- API: Similar but TypeScript-native
- Conventions: Decorators for metadata

### Key Insights
- [What to adopt]
- [What to avoid]
- [Kubit-specific adaptation needed]
```

## Common Patterns Across Frameworks

**Class-based entities:** Controllers, Models, Jobs, Mailables are classes
**Decorators/Annotations:** Metadata on properties and methods
**Convention over configuration:** Sensible defaults, override when needed
**Fluent APIs:** Method chaining for configuration
**DI containers:** Automatic dependency resolution

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aniftyco) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
