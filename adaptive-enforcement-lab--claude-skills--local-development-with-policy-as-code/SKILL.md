---
name: local-development-with-policy-as-code
description: >- Use when this capability is needed.
metadata:
  author: adaptive-enforcement-lab
---

# Local Development with Policy-as-Code

## When to Use This Skill

The policy-platform container includes all tools needed for local policy validation:

- **Kyverno CLI** - Policy validation and testing
- **Pluto** - Deprecated API detection
- **Helm** - Chart rendering and linting
- **Spectral** - OpenAPI/values schema validation
- **yq** - YAML processing

> **Zero Local Setup Required**
>
> One container contains all policies and tools. No local installations. Pull the container, run validations. Same environment as CI.
>

---


## Implementation

See the full implementation guide in the [source documentation](https://adaptive-enforcement-lab.com/enforce/policy-as-code/).


## Examples

See [examples.md](examples.md) for code examples.
## References

- [Source Documentation](https://adaptive-enforcement-lab.com/enforce/policy-as-code/)
- [AEL Enforce](https://adaptive-enforcement-lab.com/enforce/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adaptive-enforcement-lab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
