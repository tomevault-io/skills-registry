---
name: create-skill
description: Creates well-structured Claude Skills with proper YAML frontmatter, clear instructions, and supporting files. Activates when the user wants to create a new skill, add capabilities to their AI assistant, encode team preferences, or structure any reusable instruction set. Covers the full lifecycle from intent capture through writing, testing, and refinement.
metadata:
  author: terrazul-ai
---

# Create Skill

Creates Claude Skills that teach new capabilities or encode team preferences into reusable instruction sets with proper metadata, clear instructions, and supporting files.

## Two Skill Categories

### Capability Uplift

Teaches the model new abilities it doesn't have out of the box. Examples: API integration patterns, data processing pipelines, documentation generation, code migration workflows.

### Encoded Preference

Captures team conventions, style guides, review criteria, and decision frameworks so the model follows your standards consistently. Examples: brand guidelines, coding standards, PR review checklists, architecture decision records.

## Workflow

### 1. Capture Intent

Understand what the user wants the skill to do:
- What capability or convention should this skill encode?
- Who is the audience (the model, developers using the model, end users)?
- What does success look like?

### 2. Interview & Research

Ask targeted questions and study the codebase to gather context:
- Read existing code, docs, and configs relevant to the skill's domain
- Identify patterns, conventions, and anti-patterns already in use
- Ask the user about edge cases, exceptions, and priorities
- Gather concrete examples of good and bad outcomes

### 3. Write SKILL.md

Create the skill following the anatomy below. Keep the main file under ~500 lines. Push detailed specs, reference material, and extended examples into supporting files.

### 4. Test & Iterate

Try the skill with realistic requests and refine:
- Use 2-3 representative scenarios that exercise the skill's core paths
- Check that the description triggers the skill at the right time (not too broad, not too narrow)
- Verify instructions produce the desired output quality
- Adjust wording, add guardrails, or split into multiple skills if scope creeps

## Skill Anatomy

```
my-skill/
+-- SKILL.md              # Main file: metadata + instructions (<500 lines)
+-- reference.md           # Detailed specs, API references, rule catalogs
+-- examples.md            # Usage examples with input/output pairs
+-- scripts/               # Helper scripts, automation, linters
+-- references/            # Source docs, specs, PDFs-as-text
+-- assets/                # Images, templates, static files
```

Only `SKILL.md` is required. Add supporting files when the main file would exceed ~500 lines or when examples and references are extensive enough to warrant separation.

### Progressive Disclosure

- **Metadata** (frontmatter): name, description, version, allowed-tools
- **Body** (<500 lines): overview, instructions, inline examples
- **Bundled resources**: reference.md, examples.md, scripts/, references/, assets/

Keep the body focused on what the model needs to act. Move "why" explanations and exhaustive catalogs to reference.md.

## Writing Style

- **Explain "why" behind rules**, not just the rules themselves. "Use 4-space indentation because our formatter enforces it and mixed indentation breaks CI" beats "Use 4-space indentation."
- **Use imperative form.** "Use 4-space indentation" not "You should use 4-space indentation."
- **Generalize, don't overfit.** Extract the pattern from specific instances. If three API endpoints follow the same auth flow, describe the flow once and reference it.
- **Show patterns, not just instances.** A single example teaches one case; a pattern teaches a category.

## Description Optimization

Descriptions are the primary trigger mechanism. Write "pushy" descriptions that actively tell the model when to activate.

**Formula:**
```
[Verb]s [specific thing] [context] including [key features]. Activates when [trigger conditions].
```

**Requirements:**
- Third person point of view
- Maximum 1024 characters
- Include both WHAT the skill does and WHEN to use it
- End with "Activates when..." clause listing trigger conditions

**Good examples:**
- "Applies Acme Corp brand guidelines to all presentations and documents including logo usage, color palette, typography, and approved messaging. Activates when creating branded materials, reviewing documents for compliance, or answering questions about visual identity standards."
- "Processes Excel sales data and generates formatted monthly reports with charts, KPIs, and trend analysis. Activates when the user provides spreadsheet data, requests report generation, or needs data visualization from tabular files."

**Bad examples:**
- "Use this skill to apply brand guidelines" (wrong POV, no trigger clause)
- "Helps with Excel files" (too vague, no specifics, no trigger clause)

## Cross-Platform Notes

Skills are mostly compatible across Claude, Codex, and Gemini. Key differences:

| Platform | Tool Names | Skill Location | Notes |
|----------|-----------|---------------|-------|
| Claude | `Read`, `Write`, `Edit` | `.claude/skills/` | Full support including agent delegation |
| Codex | Different tool names | Shared from Claude via package config | No subagents |
| Gemini | `read_file`, `write_file` | `.gemini/skills/` | No agent delegation |

Use the `convert-skill-to-gemini` skill to adapt Claude skills for Gemini. Write instructions generically when possible to maximize cross-platform compatibility.

## Validation Checklist

Before saving, verify:
- [ ] Valid YAML frontmatter (triple dashes `---`)
- [ ] Name is lowercase with hyphens (max 64 chars, pattern: `/^[a-z0-9-]+$/`)
- [ ] Description is pushy: explains WHAT, includes "Activates when..." (< 1024 chars)
- [ ] Description uses third person ("Provides", "Generates", "Applies")
- [ ] Body stays under ~500 lines; excess moved to reference.md
- [ ] Instructions use imperative form and explain "why" behind rules
- [ ] Examples are concrete, not placeholder text
- [ ] Directory name matches the `name` field in frontmatter
- [ ] File saved in correct location (`.claude/skills/` or package `templates/claude/skills/`)
- [ ] Tested with 2-3 realistic scenarios

## Quick Start Templates

For common skill types, use these templates as starting points:
- `templates/basic-skill.md` - Generic structure
- `templates/guidelines-skill.md` - Standards/brand guidelines pattern
- `templates/process-skill.md` - Data processing workflow pattern
- `templates/tool-integration-skill.md` - External tool/API integration pattern

Invoke with: "Use the guidelines skill template"

## Reference Materials

For detailed information, consult:
- `reference.md` - Complete skill anatomy, writing patterns, and validation rules
- `examples.md` - Real-world skill examples with pattern analysis

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/terrazul-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
