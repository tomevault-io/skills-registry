---
name: osprey-migrate
description: > Use when this capability is needed.
metadata:
  author: als-apg
---

# OSPREY Migration Assistant

This skill helps you upgrade your OSPREY-based project to a newer version.

## Instructions

Follow the detailed migration workflow in [instructions.md](../../../tasks/migrate/instructions.md).

## Data Files

- **Migration documents**: [versions/](../../../tasks/migrate/versions/) - YAML files describing changes for each version
- **Schema**: [schema.yml](../../../tasks/migrate/schema.yml) - Migration document format specification

## Quick Reference

1. Ensure clean git state
2. Detect current OSPREY version
3. Load migration YAML for target version
4. Show dry-run report of all changes
5. Apply changes after user confirmation
6. Run validation commands
7. Provide summary and next steps

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/als-apg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
