---
name: documentation
description: Guides AI agents to produce accurate, well-structured technical documentation in Markdown with Mermaid diagrams. Use when this capability is needed.
metadata:
  author: jesus-seijas-sp
---

# Documentation Skill

Use this skill when asked to document code, generate architecture diagrams, explain how a module or feature works, or produce any form of technical reference material.

## When to Use

- Documenting a function, class, or module
- Generating an architecture or component overview
- Describing a feature's data flow or business logic
- Creating or updating a README
- Producing inline JSDoc/TSDoc comments

## Output Formats

- **Prose**: Always Markdown (`.md`)
- **Diagrams**: Always Mermaid (fenced ` ```mermaid ` blocks)

## Rules

This skill is governed by the following rule documents:

1. [rules/markdown-conventions.md](rules/markdown-conventions.md) — Markdown structure, heading hierarchy, tables, code blocks, and callouts.
2. [rules/mermaid-diagrams.md](rules/mermaid-diagrams.md) — When and how to use each Mermaid diagram type, quality checklist, and examples.
3. [rules/documentation-workflow.md](rules/documentation-workflow.md) — Step-by-step process: read code → choose scope → write docs → output.

## Quick Reference

| Scope | Primary structure | Diagram type |
|-------|------------------|--------------|
| Function / method | API table (params, returns, example) | None unless complex |
| Module / file | Purpose + exports + internal structure | `graph LR` or `classDiagram` |
| Feature / flow | Overview + flow + key components | `sequenceDiagram` or `flowchart TD` |
| Full project | Architecture overview + module map | `graph LR` |

---
> Source: [jesus-seijas-sp/claude-node-setup](https://github.com/jesus-seijas-sp/claude-node-setup) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
