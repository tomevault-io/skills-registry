---
name: skill-creator
description: Design, create, audit, and optimize OpenCode skills using a quality-first workflow with dry-run, quality gates, and structured references. Use when users ask to build or improve a skill. Do not trigger for generic OpenCode Q&A or unrelated coding tasks. Use when this capability is needed.
metadata:
  author: digi4care
---

# Skill Creator

I create and improve OpenCode skills with a quality-first process based on workplace best practices.

## When to Use Me

Use me when:

- you want to create a new OpenCode skill
- you want to optimize or refactor an existing SKILL.md
- you want to improve triggering quality, structure, or error handling
- you want to add references and a registry for progressive disclosure

Do not use me for:

- general OpenCode documentation Q&A
- non-skill plugin-only implementation work
- unrelated programming/debugging tasks

## Workflow

1. Classify request as `plan`, `audit`, `create`, or `optimize`.
2. Validate scope, target path, and required inputs (purpose, triggers, workflow, error handling, tests).
3. Start with dry-run output and show planned changes.
4. Apply only after explicit confirmation.
5. Run quality checks and accept changes only when quality does not regress.
6. Return a concise report with changed files, quality delta, and next steps.

## Execution Rules

- Default to dry-run.
- Keep `SKILL.md` concise and move depth into `references/`.
- Use explicit trigger phrases and negative triggers.
- Include concrete error handling and test prompts.
- Preserve existing high-quality content when optimizing.

## Tool Routing

Primary tools:

- `skill-creator-plan`
- `skill-creator-audit`
- `skill-creator-create`
- `skill-creator-optimize`

Command wrappers:

- `/skill-creator-plan`
- `/skill-creator-audit`
- `/skill-creator-create`
- `/skill-creator-optimize`

If tools are unavailable, use the templates in references and return a manual plan instead of guessing.

## Cross-Skill Handoffs

- Route to `meta-agent` when the request is primarily component choice across command/agent/plugin/skill architecture.
- Route to `opencode-mastery` when the request is documentation-only Q&A without skill file changes.
- Keep requests in `skill-creator` when the user asks to create, audit, optimize, or refactor SKILL artifacts.

## Error Handling

- Missing required input: ask one targeted question and provide a safe default.
- Invalid path or unsafe write target: block and explain the path constraint.
- Quality score regression: block apply and return before/after metrics.
- Missing registry or references: create minimal compliant files when confirmed.

## Quick Tests

Should trigger:

- "Create a new skill for incident postmortem writing."
- "Optimize this SKILL.md to improve triggering and error handling."
- "Audit this skill against best practices."

Should not trigger:

- "How do I configure OpenCode providers?"
- "Fix this React state bug."

Functional:

- "Refactor `src/skill/frontend-design/SKILL.md` with dry-run first, then apply only if quality score improves."

## References

- `references/workflow-playbook.mdx`
- `references/quality-rubric.mdx`
- `references/templates.mdx`
- `references/troubleshooting.mdx`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/digi4care) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
