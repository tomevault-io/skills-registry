---
name: implement
description: > Use when this capability is needed.
metadata:
  author: martinellich
---

# Implement Use Case

## Instructions

Implement the use case $ARGUMENTS using Vaadin for the UI layer and jOOQ for data access.
Don't create tests – there are the `karibu-test` and `playwright-test` skills for that.

Check the Vaadin and jOOQ MCP servers for guidance.

## DO NOT

- Create test classes (use dedicated testing skills instead)

## Workflow

1. Read the use case specification from `docs/use_cases/`
2. Read the entity model from `docs/entity_model.md`
3. Check existing code for patterns and conventions
4. Implement the data access layer using jOOQ
5. Verify the data access layer compiles and follows existing patterns
6. Implement the Vaadin view following existing patterns
7. Wire up the view with the data access layer
8. Verify the full implementation compiles successfully

## Resources

- Use the Vaadin MCP server for component documentation
- Use the jOOQ MCP server for query DSL reference
- Use the JavaDocs MCP server for API documentation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/martinellich) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
