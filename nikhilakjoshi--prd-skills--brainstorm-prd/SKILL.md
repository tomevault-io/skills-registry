---
name: brainstorm-prd
description: Take raw ideas/requirements in any form, analyze the current project, ask clarifying questions, and produce a detailed PRD document. Use when this capability is needed.
metadata:
  author: nikhilakjoshi
---

# brainstorm-prd

Take raw ideas/requirements in any form, analyze the project, ask clarifying questions, then produce a detailed PRD.

## Inputs

- **Ideas/Requirements**: PRD doc, raw ideas, bullet points, conversation, sketches -- any form
- **Repo**: the project repo to ground the PRD in

## Process

1. Read/parse the provided ideas or requirements
2. Explore the repo -- understand architecture, key files, frameworks, existing code patterns
3. Ask clarifying questions using the AskUserQuestion tool before finalizing. Cover ambiguities in scope, edge cases, priorities, technical constraints, UX expectations.
4. Synthesize answers + repo context into a detailed PRD
5. Write the PRD to `{project}/plans/prd.md`

## PRD structure

The output PRD should include:

- **Overview**: what the feature/product is and why it matters
- **Goals & non-goals**: explicit scope boundaries
- **User stories / use cases**: who benefits and how
- **Functional requirements**: detailed behavior specs grounded in the actual codebase
- **Technical considerations**: architecture, dependencies, constraints informed by repo exploration
- **Edge cases & error handling**
- **Open questions**: anything unresolved after clarification

## Rules

- **Explore first**: always read the repo before writing the PRD. Reference real files, components, routes, functions where relevant.
- **Ask before finalizing**: always ask clarifying questions before writing the PRD. Don't assume -- surface ambiguities, edge cases, missing details.
- **Ground in reality**: tie requirements back to the actual codebase. Mention specific files, patterns, frameworks discovered during exploration.
- **Be detailed**: the PRD should be thorough enough that an engineer can implement from it without further clarification.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nikhilakjoshi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
