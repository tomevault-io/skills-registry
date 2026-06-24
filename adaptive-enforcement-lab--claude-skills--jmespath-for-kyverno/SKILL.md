---
name: jmespath-for-kyverno
description: >- Use when this capability is needed.
metadata:
  author: adaptive-enforcement-lab
---

# JMESPath for Kyverno

## When to Use This Skill

**Use JMESPath when:**

- Pattern matching can't express your logic
- You need conditionals or transformations
- Validation depends on multiple fields
- You're filtering or comparing arrays

**Skip JMESPath when:**

- Simple pattern matching works (`pattern`, `anyPattern`)
- You're only checking field existence
- No cross-field validation needed

> **Test Before Deploying**
>
> Always test JMESPath expressions with `kyverno jp` before adding them to policies. Syntax errors fail silently in audit mode and block resources in enforce mode.
>

---


## Implementation

**Install Kyverno CLI for testing:**

```bash
# Install kyverno CLI
brew install kyverno/kyverno/kyverno

# Test JMESPath expression
kyverno jp query -i manifest.yaml 'spec.template.spec.containers[*].name'
```

**Simple validation example:**


*See [examples.md](examples.md) for detailed code examples.*

**What this does:**

- Filters containers without memory limits: `containers[?!resources.limits.memory]`
- Extracts their names: `.name`
- Counts them: `| length(@)`
- Denies if count > 0

---


## Examples

See [examples.md](examples.md) for code examples.
## References

- [Source Documentation](https://adaptive-enforcement-lab.com/enforce/policy-as-code/)
- [AEL Enforce](https://adaptive-enforcement-lab.com/enforce/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adaptive-enforcement-lab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
