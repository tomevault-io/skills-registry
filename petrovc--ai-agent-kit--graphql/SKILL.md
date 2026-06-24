---
name: graphql
description: > Use when this capability is needed.
metadata:
  author: PetrovC
---

# GraphQL Skill

## Goal
Implement correct, performant, and evolvable GraphQL APIs.
The schema is the contract — design it for consumers, not for the database.

## Quick reference

| Concept | Best practice |
|---|---|
| Schema | Design schema first, use descriptive names, avoid breaking changes |
| N+1 Problem | Always use DataLoaders for batching child entity resolution |
| Security | Disable introspection in production, enforce query depth & cost limits |
| Client | Use codegen for frontend types, use variables for dynamic inputs |
| Validation | Validate query syntax, variables, and custom scalar inputs |

## Full guidance
Extended how-to, patterns, anti-patterns, and checklists: [`SKILL.deep.md`](SKILL.deep.md)

---
> Source: [PetrovC/ai-agent-kit](https://github.com/PetrovC/ai-agent-kit) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
