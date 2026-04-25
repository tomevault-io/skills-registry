---
name: skill-creator
description: > Use when this capability is needed.
metadata:
  author: 333-333-333
---

## When to Create a Skill

Create a skill when:
- A pattern is used repeatedly and AI needs guidance
- Project-specific conventions differ from generic best practices
- Complex workflows need step-by-step instructions
- Decision trees help AI choose the right approach

**Don't create a skill when:**
- Documentation already exists (create a reference instead)
- Pattern is trivial or self-explanatory
- It's a one-off task

---

## Skill Structure

```
skills/{skill-name}/
├── SKILL.md              # Required - main skill file
├── assets/               # Optional - templates, schemas, examples
│   ├── template.py
│   └── schema.json
└── references/           # Optional - links to local docs
    └── docs.md           # Points to docs/developer-guide/*.mdx
```

---

## SKILL.md Template

```markdown
---
name: {skill-name}
description: >
  {One-line description of what this skill does}.
  Trigger: {When the AI should load this skill}.
metadata:
  author: 333-333-333
  version: "1.0"
  type: {generic|project|meta}
  scope: [{root|directory-name}]
  auto_invoke:
    - "{Action that triggers this skill}"
    - "{Another action that triggers this skill}"
---

## When to Use

{Bullet points of when to use this skill}

## Critical Patterns

{The most important rules - what AI MUST know}

## Code Examples

{Minimal, focused examples}

## Commands

```bash
{Common commands}
```

## Resources

- **Templates**: See [assets/](assets/) for {description}
- **Documentation**: See [references/](references/) for local docs
```

---

## Naming Conventions

| Type | Pattern | Examples |
|------|---------|----------|
| Generic skill | `{technology}` or `{tech}-{concern}` | `typescript`, `go-tdd`, `go-gin-handlers` |
| Project-specific | `{project}-{component}` | `bastet-booking`, `bastet-auth` |
| Workflow skill | `{action}-{target}` | `skill-creator`, `skill-sync` |
| Meta skill | `skill-{action}` | `skill-creator`, `skill-sync` |

---

## Decision: Skill Type

```
Patterns apply to ANY project?         → type: generic
Patterns are specific to THIS repo?    → type: project
Skill manages the skills system?       → type: meta
```

**Important**: The `type` field determines where the skill appears in AGENTS.md:
- `generic` → **Generic Skills** table (reusable across projects)
- `project` → **Project Skills** table (repo-specific)
- `meta` → **Meta-Skills** section (manually managed, not synced)

---

## Decision: assets/ vs references/

```
Need code templates?        → assets/
Need JSON schemas?          → assets/
Need example configs?       → assets/
Link to existing docs?      → references/
Link to external guides?    → references/ (with local path)
```

**Key Rule**: `references/` should point to LOCAL files (`docs/developer-guide/*.mdx`), not web URLs.

---

## Frontmatter Fields

| Field | Required | Description |
|-------|----------|-------------|
| `name` | Yes | Skill identifier (lowercase, hyphens) |
| `description` | Yes | What + Trigger in one block |
| `metadata.author` | Yes | `333-333-333` |
| `metadata.version` | Yes | Semantic version as string |
| `metadata.type` | Yes | `generic`, `project`, or `meta` |
| `metadata.scope` | Yes | Array of scopes: `[root]`, `[api]`, `[root, api]`, etc. |
| `metadata.auto_invoke` | Yes | String or list of actions that trigger this skill |

### Scope Values

| Scope | Updates |
|-------|---------|
| `root` | `./AGENTS.md` (repository root) |
| `{directory}` | `./{directory}/AGENTS.md` |

A skill can target multiple scopes: `scope: [root, api, web]`

### Auto-invoke Format

Single action:
```yaml
auto_invoke: "Creating new components"
```

Multiple actions:
```yaml
auto_invoke:
  - "Creating new components"
  - "Refactoring component structure"
```

---

## Content Guidelines

### DO
- Start with the most critical patterns
- Use tables for decision trees
- Keep code examples minimal and focused
- Include Commands section with copy-paste commands

### DON'T
- Add Keywords section (agent searches frontmatter, not body)
- Duplicate content from existing docs (reference instead)
- Include lengthy explanations (link to docs)
- Add troubleshooting sections (keep focused)
- Use web URLs in references (use local paths)

---

## Registering the Skill

After creating the skill, run the sync script to register it in AGENTS.md:

```bash
./skills/skill-sync/assets/sync.sh
```

This automatically updates the skill tables and auto-invoke sections based on `metadata.type`, `metadata.scope`, and `metadata.auto_invoke`.

---

## Checklist Before Creating

- [ ] Skill doesn't already exist (check `skills/`)
- [ ] Pattern is reusable (not one-off)
- [ ] Name follows conventions
- [ ] Frontmatter is complete:
  - [ ] `name` and `description` (with Trigger)
  - [ ] `metadata.type` (generic/project/meta)
  - [ ] `metadata.scope` (where to register)
  - [ ] `metadata.auto_invoke` (when to trigger)
- [ ] Critical patterns are clear
- [ ] Code examples are minimal
- [ ] Commands section exists
- [ ] Ran `./skills/skill-sync/assets/sync.sh`

## Resources

- **Templates**: See [assets/](assets/) for SKILL.md template

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/333-333-333) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
