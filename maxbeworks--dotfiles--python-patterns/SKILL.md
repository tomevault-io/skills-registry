---
name: python-patterns
description: Python coding style - pragmatic, readable, fast development over heavy abstraction Use when this capability is needed.
metadata:
  author: maxbeworks
---

## Philosophy

- Python is for quick and dirty solutions - prioritize speed and readability over abstraction layers
- Don't overcomplicate with rarely-used built-in methods or excessive safety measures
- Write code that's easy to read and understand, not "clever"

## Readability and Style

- Use one-liners when they're clear and simple
- Break into multiple lines when complexity makes it hard to parse
- Best judgment on what's "too complex" - if you have to think twice, split it
- Prefer explicit over implicit when it improves clarity

## Type Annotations

- Only use typing and Pydantic when truly necessary
- API endpoints, request/response models - yes, type these
- Internal utility functions, scraping scripts, quick tools - skip typing unless it adds real value
- Don't type just because "best practices" say so - use judgment based on whether the code is static/stable

## Error Handling

- Never use bare `except:` - always specify exception types
- Handle specific exceptions you expect, let others bubble up
- Early returns for error cases

## Class Design

- Prefix internal methods with `_` (single underscore) when they're only meant for internal class use
- Keep classes simple - don't create unnecessary hierarchies
- Composition over inheritance when it makes sense

## Code Organization

- Keep functions focused but don't obsess over single responsibility
- Split when readability suffers, not by arbitrary rules
- Colocate related functionality
- Flat is better than nested

## Anti-Patterns

- Over-engineering with abstract base classes and complex inheritance
- Type hints on everything just to have them
- Deeply nested comprehensions that require mental gymnastics
- Magic methods unless you have a damn good reason
- Creating classes when simple functions would work fine

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/maxbeworks) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
