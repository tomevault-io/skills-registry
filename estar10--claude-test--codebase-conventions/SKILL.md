---
name: codebase-conventions
description: Use when first working in the codebase or when discovering a new pattern or convention. Documents project conventions for consistency.
metadata:
  author: estar10
---

When you notice a recurring pattern or convention in the codebase:

1. **Check `.claude/conventions.md`** -- Does this convention already exist there?
2. **If not, add it** -- Append a short entry describing the convention:

```
### [Category]: [Convention name]
- **Pattern:** [What the convention looks like]
- **Example:** [File or code reference]
- **When to use:** [When this pattern applies]
```

Categories include: naming, file structure, error handling, testing, imports, API design, etc.

Always read `.claude/conventions.md` before making changes to ensure consistency with established patterns. Only document conventions that are actually present in the code -- do not invent or prescribe new ones.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/estar10) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
