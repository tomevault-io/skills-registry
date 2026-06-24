---
name: global-validation
description: This Skill provides Claude Code with specific guidance on how to adhere to coding standards as they Use when this capability is needed.
metadata:
  author: adigunners
---

# Global Validation

This Skill provides Claude Code with specific guidance on how to adhere to coding standards as they
relate to how it should handle global validation in the adigunners.github.io static website project.

## When to use this skill

- Validating JSON data structure when loading data files from the `data/` directory before using
  them
- Checking that required properties exist in data objects before rendering content to the page
- Validating user input from HTML forms to ensure data meets expected formats and constraints
- Verifying that DOM elements exist before attempting to manipulate them in JavaScript modules
- Validating data types and value ranges (e.g., ensuring scores are numbers, names are strings)
  before processing
- Checking CSS class names and ID selectors to ensure referenced elements exist in the HTML
- Validating file paths and URLs before attempting to fetch external resources in the static site

## Instructions

For details, refer to the information provided in this file:
[global validation](../../../agent-os/standards/global/validation.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adigunners) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
