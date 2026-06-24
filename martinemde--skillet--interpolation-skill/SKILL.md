---
name: interpolation-skill
description: Tests variable interpolation features like {baseDir}. Use for testing path expansion.
metadata:
  author: martinemde
---

# Interpolation Skill

This skill demonstrates variable interpolation.

## Base Directory

The base directory for this skill is: {baseDir}

## Instructions

When invoked, this skill should:

1. Read configuration from {baseDir}/config.json
2. Load reference data from {baseDir}/references/data.txt
3. Execute scripts from {baseDir}/scripts/process.sh

## File References

See [the reference guide]({baseDir}/references/REFERENCE.md) for details.

Run the extraction script:
```bash
{baseDir}/scripts/extract.py
```

## Notes

All paths using {baseDir} should be expanded to the absolute path
of the directory containing this SKILL.md file.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/martinemde) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
