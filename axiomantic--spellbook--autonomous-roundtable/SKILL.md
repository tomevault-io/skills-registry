---
name: autonomous-roundtable
description: | Use when this capability is needed.
metadata:
  author: axiomantic
---

# Autonomous Roundtable (Deprecated)

<CRITICAL>
This skill is deprecated. Its functionality has been absorbed into the `develop` skill.

**Migration:**
- Project decomposition: Use develop with COMPLEX tier (automatic work item decomposition)
- Roundtable validation: Set `dialectic_mode: "roundtable"` in Phase 0.4 preferences
- Token enforcement: Set `token_enforcement: "gate_level"` or `"every_step"` in Phase 0.4
- Reflexion on ITERATE: Still invoked automatically by develop when roundtable returns ITERATE

**To use:** Invoke the `develop` skill instead. Configure dialectic preferences in Phase 0.4.
</CRITICAL>

## Invariant Principles

1. **Deprecated** - Do not use this skill. Use `develop` instead.

<analysis>This skill is deprecated. Redirect to develop skill.</analysis>
<reflection>If invoked, redirect user to the develop skill with dialectic_mode: "roundtable".</reflection>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/axiomantic) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
