---
name: reality-check
description: Quick documentation vs code reality validation. Use when this capability is needed.
metadata:
  author: fall-out-bug
---

# @reality-check

Quick validation that docs match code before making changes. ~90 seconds (vs 5-10 min for @verify-workstream).

## When to Use

- About to modify file based on documentation
- Unsure if docs reflect implementation
- Quick check before full verify-workstream

## Workflow

1. **Read actual code first** — Don't read docs first. Use Read tool on target file.
2. **Compare** — What does code actually do vs what we assumed?
3. **Report** — Match? Proceed. Mismatch? Stop or adapt.

## Output Format

```markdown
## Reality Check: <filename>
### What Code Actually Does: [summary]
### What We Assumed: [expectation]
### Recommendation: ✅ Proceed / ⚠️ Stop / 🔄 Adapt
```

## See Also

- @verify-workstream — Full workstream validation
- @build — Uses reality-check during execution

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fall-out-bug) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
