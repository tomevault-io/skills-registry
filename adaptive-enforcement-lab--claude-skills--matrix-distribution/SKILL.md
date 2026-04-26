---
name: matrix-distribution
description: >- Use when this capability is needed.
metadata:
  author: adaptive-enforcement-lab
---

# Matrix Distribution

## When to Use This Skill

> **Poor Fit**
>
>
> - Sequential operations where order matters
> - Operations with shared state between targets
> - When total job count would exceed GitHub Actions limits (256)
>

---


## Implementation

### Dynamic Matrix

Generate the target list in a discovery stage:


*See [examples.md](examples.md) for detailed code examples.*

### Failure Isolation

Prevent one failure from canceling other jobs:

```yaml
strategy:
  matrix:
    target: ${{ fromJson(needs.discover.outputs.targets) }}
  fail-fast: false  # Critical: continue processing other targets
```

### Rate Limiting

Control concurrency to avoid API rate limits:

```yaml
strategy:
  matrix:
    target: ${{ fromJson(needs.discover.outputs.targets) }}
  max-parallel: 10  # Limit concurrent jobs
```

---


## Examples

See [examples.md](examples.md) for code examples.
## References

- [Source Documentation](https://adaptive-enforcement-lab.com/patterns/architecture/)
- [AEL Patterns](https://adaptive-enforcement-lab.com/patterns/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adaptive-enforcement-lab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
