---
name: terraform-modules
description: Design and implement reusable Terraform modules with proper interfaces, testing, and documentation. Use when creating or refactoring infrastructure modules. Use when this capability is needed.
metadata:
  author: ihatesea69
---

# Terraform Modules

Activate this skill when designing or implementing Terraform modules.

## When to Use

- Creating reusable infrastructure components
- Refactoring inline resources into modules
- Designing module interfaces (variables, outputs)
- Testing infrastructure code
- Publishing modules to registries

## Module Structure

```
modules/<module-name>/
  main.tf           Primary resource definitions
  variables.tf      Input variables with descriptions and validation
  outputs.tf        Output values for consumers
  versions.tf       Provider and Terraform version constraints
  README.md         Usage documentation with examples
  examples/         Example configurations
  tests/            Terratest or terraform test files
```

## Design Principles

- Single responsibility: one module, one concern
- Sensible defaults: work out of the box with minimal config
- Explicit interfaces: all inputs documented with types and validation
- Composable: modules should compose with other modules cleanly
- Testable: include examples that can be used as integration tests

## Rules

- Pin provider versions in versions.tf
- Add validation blocks to variables where appropriate
- Use `terraform fmt` before committing
- Include at least one working example
- Document all variables and outputs
- Use data sources over hardcoded values
- Never hardcode account IDs, regions, or environment names

---
> Source: [ihatesea69/kiro-kit](https://github.com/ihatesea69/kiro-kit) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
