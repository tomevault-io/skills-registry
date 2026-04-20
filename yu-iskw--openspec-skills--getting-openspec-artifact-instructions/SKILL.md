---
name: getting-openspec-artifact-instructions
description: Outputs enriched instructions for creating an artifact or applying tasks for an OpenSpec change. Use when the agent needs guidance on how to write a spec, design, or implement tasks. Use when this capability is needed.
metadata:
  author: yu-iskw
---

# Getting OpenSpec Artifact Instructions

## Purpose

Provides the AI agent with specific, context-aware instructions for the next step in the OpenSpec workflow.

## 1. Safety & Verification

1.  **Check Help**: Run `npx @fission-ai/openspec@latest instructions --help`.

## 2. Common Workflows

### Workflow: Get Instructions for a Specific Artifact

1.  Run `npx @fission-ai/openspec@latest instructions <artifact-name> --change <change-name>`.
    Example artifacts: `proposal`, `specs`, `design`, `tasks`.

### Workflow: Get Implementation Instructions

1.  Run `npx @fission-ai/openspec@latest instructions apply --change <change-name>`.
    This provides guidance on how to implement the tasks defined in the change proposal.

### Workflow: Get Instructions with Staff Context

To ensure the artifact is drafted with staff-level rigor:

1.  Run the standard `instructions` command as above.
2.  **Enrichment**: Supplement the CLI output with the following "Staff Context" questions:
    - "How does this artifact address the long-term architectural health of the project?"
    - "What are the cross-disciplinary implications (UX, Performance, Security) for this specific artifact?"
    - "Can we simplify this design to improve ROI without compromising core requirements?"

## 3. Examples

### Example: Get Instructions for Writing Specs

**Command**: `npx @fission-ai/openspec@latest instructions specs --change my-feature`
**Expected Output**: Detailed guidance on the structure and content expected for the specifications of `my-feature`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yu-iskw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
