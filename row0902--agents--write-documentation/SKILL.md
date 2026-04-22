---
name: write-documentation
description: Diátaxis framework templates and rules for creating documentation in English. Use when this capability is needed.
metadata:
  author: row0902
---

# 📝 Documentation Skill (Diátaxis)

## Context
All documentation must be in **English** and follow the **Diátaxis** framework.

## 1. Architecture Reference (File: `docs/reference/module_name.md`)

```markdown
# Reference: [Module Name]

> **Type:** Technical Reference
> **Audience:** Developers

Brief description of what this module provides.

## ⚙️ API Usage

::: package.module_name
    options:
      show_root_heading: true
      show_source: false
```

## 2. How-To Guide (File: `docs/guides/how_to_task.md`)

```markdown
# How-to: [Action Name]

> **Type:** Guide
> **Goal:** [Specific goal]

## 🪜 Prerequisites
*   [ ] Requirement 1
*   [ ] Requirement 2

## 🚀 Steps

1.  **Step One:**
    Arguments or commands to run.
    
    ```python
    # Example code
    ```

2.  **Step Two:**
    Description.
```

## 3. Explanation/Concept (File: `docs/topics/concept_name.md`)

```markdown
# Concept: [Topic Name]

> **Type:** Explanation

Deep dive into *why* something works the way it does. Not instructions, but understanding.
```

## Checklist
*   [ ] **English Only?**
*   [ ] **Absolute Paths for Images?**
*   [ ] **Correct Directory?** (`docs/reference`, `docs/guides`, etc.)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/row0902) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
