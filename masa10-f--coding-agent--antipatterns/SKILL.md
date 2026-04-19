---
name: antipatterns
description: This skill should be used automatically during code review and implementation to prevent common coding mistakes and antipatterns. It helps agents avoid known bad practices that have been identified through past experiences. Use when this capability is needed.
metadata:
  author: masa10-f
---

# Antipatterns Skill

## Overview

This skill contains a collection of coding antipatterns that agents should avoid. Each antipattern is documented in the `patterns/` directory and should be consulted during code review and implementation tasks.

**When this skill applies:**
- Code review tasks
- Writing new code
- Refactoring existing code
- Any implementation work

## How to Use

When performing code review or implementation:

1. Review all pattern files in `patterns/` directory
2. Check if the code being written or reviewed violates any antipattern
3. If a violation is found, fix it according to the guidance provided

## Current Antipatterns

The following antipatterns are currently documented:

| ID | Pattern | Category |
|----|---------|----------|
| AP-001 | Avoid `object` type in Python | Python/Typing |
| AP-002 | Don't modify pyproject.toml unnecessarily | Configuration |
| AP-003 | Use collections.abc for generic types | Python/Typing |
| AP-004 | Reuse existing enums | Code Reuse |
| AP-005 | Always add unit tests for new features | Testing |
| AP-006 | Use generic types for input, concrete types for output | Python/Typing |

## Adding New Antipatterns

To add a new antipattern:

1. Create a new markdown file in `patterns/` directory
2. Follow the naming convention: `AP-XXX-short-description.md`
3. Use the template structure:
   ```markdown
   # AP-XXX: Title

   ## Category
   [Category name]

   ## Description
   [What the antipattern is]

   ## Why It's Bad
   [Why this should be avoided]

   ## Correct Approach
   [How to do it correctly]

   ## Examples

   ### Bad
   [Code example of the antipattern]

   ### Good
   [Code example of the correct approach]
   ```

4. Update this SKILL.md to include the new antipattern in the table above

## Pattern Files

All antipattern definitions are stored in:
- `patterns/AP-001-avoid-object-type.md`
- `patterns/AP-002-no-pyproject-modification.md`
- `patterns/AP-003-use-collections-abc.md`
- `patterns/AP-004-reuse-enums.md`
- `patterns/AP-005-add-unit-tests.md`
- `patterns/AP-006-generic-input-concrete-output.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/masa10-f) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
