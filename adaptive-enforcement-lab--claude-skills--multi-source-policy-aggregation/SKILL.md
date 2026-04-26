---
name: multi-source-policy-aggregation
description: >- Use when this capability is needed.
metadata:
  author: adaptive-enforcement-lab
---

# Multi-Source Policy Aggregation

## When to Use This Skill

Real-world policy management requires aggregating policies from different teams and sources:

```mermaid
graph TD
    SP[security-policy repo] -->|OCI container| MS[Multi-stage Build]
    DP[devops-policy repo] -->|OCI container| MS
    KC[Kyverno curated] -->|OCI container| MS
    APP[Application repo] -->|OCI container| MS

    MS -->|Single image| PP[policy-platform:latest]

    PP -->|Contains all policies| OUT[Unified Enforcement]

    %% Ghostty Hardcore Theme
    style SP fill:#f92572,color:#1b1d1e
    style DP fill:#fd971e,color:#1b1d1e
    style KC fill:#9e6ffe,color:#1b1d1e
    style APP fill:#a7e22e,color:#1b1d1e
    style PP fill:#65d9ef,color:#1b1d1e

```

> **Policy Repos as OCI Containers**
>
> Each policy repository is **also** an OCI container. Multi-stage Docker builds pull them all automatically. No manual copying or Git submodules.
>

---


## Implementation

See the full implementation guide in the [source documentation](https://adaptive-enforcement-lab.com/enforce/policy-as-code/).


## Examples

See [examples.md](examples.md) for code examples.


## Full Reference

See [reference.md](reference.md) for complete documentation.
## References

- [Source Documentation](https://adaptive-enforcement-lab.com/enforce/policy-as-code/)
- [AEL Enforce](https://adaptive-enforcement-lab.com/enforce/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adaptive-enforcement-lab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
