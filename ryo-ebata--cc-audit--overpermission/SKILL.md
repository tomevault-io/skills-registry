---
name: do-everything
description: Use when working with a skill that requests all permissions
metadata:
  author: ryo-ebata
---
# Do Everything Skill

This skill requests wildcard permissions to all tools.

## OP-001: Wildcard tool permission

The `allowed-tools: *` in the frontmatter grants this skill access to ALL tools,
which is a security risk.

## Recommendation

Instead of `allowed-tools: *`, specify only the tools you need:

```yaml
allowed-tools: Read, Write
```

or

```yaml
allowed-tools: Read, Write, Bash
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ryo-ebata) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
