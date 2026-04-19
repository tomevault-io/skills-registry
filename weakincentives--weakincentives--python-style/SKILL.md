---
name: python-style
description: Apply Python best practices when reviewing or writing code, including PEP 8, type annotations, docstrings, and common anti-patterns to avoid. Use when this capability is needed.
metadata:
  author: weakincentives
---

# Python Style Skill

Apply Python best practices when reviewing or writing code.

## Style Guidelines

- Follow PEP 8 for formatting
- Use type annotations for all public functions (PEP 484)
- Write docstrings for public APIs (PEP 257)
- Prefer f-strings over .format() or % formatting

## Common Issues to Flag

- Missing type annotations on public functions
- Mutable default arguments (def foo(items=[]))
- Bare except clauses (except: instead of except Exception:)
- Using assert for validation (stripped in optimized mode)

## References

- PEP 8: https://peps.python.org/pep-0008/
- PEP 484: https://peps.python.org/pep-0484/
- PEP 257: https://peps.python.org/pep-0257/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/weakincentives) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
