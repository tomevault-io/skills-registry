---
name: python
description: Use when working with a language that lets you work quickly and integrate systems more effectively
metadata:
  author: sami
---

# Python Skill

## Best Practices
1.  **PEP 8**: Follow standard style guide (Snake case for vars, 4 spaces indent).
2.  **Type Hints**: Use `typing` module (List, Optional, etc.) for robust code.
3.  **Virtual Envs**: Always use `venv` or `poetry`. Never install global pip packages.
4.  **Exceptions**: Catch specific exceptions (`ValueError`), never bare `except:`.

## Common Pitfalls
*   **Mutable Defaults**: `def func(list=[]):` causes bugs. Use `None` instead.
*   **GIL**: Threads don't speed up CPU tasks. Use `multiprocessing`.

## References
*   [Python Docs](https://docs.python.org/3/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sami) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
