---
name: manage-cursor-rules
description: Create, edit, validate, and organize Cursor rules in .cursor/rules/ directory. Use when the user wants to create rules, add coding standards, set up project conventions, configure file-specific patterns, validate rule structure, or asks about .mdc files or .cursor/rules/. Use when this capability is needed.
metadata:
  author: kscius
---

> **On-demand loading:** Read this skill only when the task clearly matches the description above. Load the `references/*.md` files only when you need the detailed templates, examples, or catalogs they contain. Do not load for unrelated work.

# Manage Cursor Rules

Create, edit, validate, and organize Cursor rules — the `.mdc` files that provide persistent AI guidance for a project.

## When to Use

- The user wants to create a new Cursor rule or coding standard
- An existing rule needs editing while preserving its structure
- A rule's format/metadata/content needs validating
- Rules need organizing, categorizing, or cross-referencing
- The user asks about `.mdc` files or the `.cursor/rules/` directory

## Core Essentials

Every rule is an `.mdc` file with YAML frontmatter:

```markdown
---
description: Short description of when/how the rule applies
globs: optional/path/pattern/**/*   # limits rule to matching files
alwaysApply: false                  # true = applies to all files
---
# Rule Title

Guidance, do's and don'ts, good/bad examples, cross-references.
```

Non-negotiable rules:

- **Location**: files MUST live in `PROJECT_ROOT/.cursor/rules/` — never the project root, source dirs, or `/docs/`.
- **Naming**: kebab-case + `.mdc` extension, descriptive (e.g. `api-conventions.mdc`).
- **Scope**: one concern per rule. Use `alwaysApply: true` for project-wide standards, `globs` for targeted guidance.
- **Descriptions**: specific, actionable, context-aware — not vague.

## Workflow

### Creating a rule

1. **Determine details** — purpose, scope (always vs glob), category.
2. **Choose scope** — `alwaysApply: true` or a `globs` pattern.
3. **Write a specific description** — exactly what it covers and when it applies.
4. **Structure content** — overview, guidelines, good/bad examples, related-rule links.
5. **Validate** against the checklist before saving.

→ Full steps, scope options, and ready-to-use templates: `references/templates.md`.

### Editing a rule

1. Read the full rule first (never edit blind).
2. Identify the change (examples, scope, description, cross-refs).
3. Preserve structure — **never** remove the frontmatter.
4. Re-validate after editing.

→ Detailed editing steps and worked workflows: `references/examples.md`.

### Validating a rule

Check in order: location (`.cursor/rules/`) → extension (`.mdc`) → frontmatter delimiters → required fields (`description`, `alwaysApply`) → H1 title → fenced code examples.

→ Full structure spec, metadata table, and validation checklist: `references/frontmatter-reference.md`.

### Organizing rules

Group by category (style, architecture, security, docs, testing, meta), use numbered prefixes (`00-`…`99-`) for ordering, and cross-reference related rules for discoverability.

→ Categories, naming strategy, and maintenance cadence: `references/organizing.md`.

## Content Quality

- Show **good vs bad** examples for every guideline.
- Cross-reference project files with the `mdc:` protocol, e.g. `[auth.service.ts](mdc:src/services/auth.service.ts)`.
- Keep each rule narrow — one concern.

→ Best practices, anti-patterns, and full worked examples: `references/examples.md`.

## References

- `references/frontmatter-reference.md` — rule structure, required metadata table, file location, naming, validation checklist.
- `references/templates.md` — detailed creation steps, security/style/architecture rule templates, command sequences.
- `references/examples.md` — content best practices, anti-patterns, end-to-end workflow examples, editing steps.
- `references/organizing.md` — rule categories, file-naming strategy, cross-referencing, maintenance schedule.

---
> Source: [kscius/KS-Cursor-Orchestrator](https://github.com/kscius/KS-Cursor-Orchestrator) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
