---
name: python-dependency-resolver
description: Analyze and resolve Python package dependency conflicts. Use when pip install fails due to version mismatches or circular dependencies. Use when this capability is needed.
metadata:
  author: jorgealves
---
# Python Dependency Resolver

## Purpose and Intent
Analyze and resolve Python package dependency conflicts. Use when pip install fails due to version mismatches or circular dependencies.

## When to Use
- **Project Setup**: When initializing a new Python project.
- **Continuous Integration**: As part of automated build and test pipelines.
- **Legacy Refactoring**: When updating older Python codebases to modern standards.

## When NOT to Use
- **Non-Python Projects**: This tool is specialized for the Python ecosystem.

## Error Conditions and Edge Cases
- **Missing Requirements**: If the project lacks a requirements.txt or pyproject.toml.
- **Incompatible Versions**: If the project uses a Python version not supported by the tools.

## Security and Data-Handling Considerations
- All analysis is performed locally.
- No source code or credentials are ever transmitted externally.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jorgealves) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
