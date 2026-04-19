---
name: update-docs
description: Update project documentation after feature development or code changes. Use when adding features, modifying functionality, changing project structure, updating configuration, or when any documentation needs to reflect recent changes. Use when this capability is needed.
metadata:
  author: roo4l
---

# Updating Project Documentation

## Documentation Structure

The project has a layered documentation structure. Understand it before making changes.

### Root files

Standard GitHub-recognized files that stay at the project root:

| File | Purpose | Update when... |
|------|---------|----------------|
| `README.md` | Project overview, "What You'll Find Here", links | Features visible to users change, new top-level sections added |
| `CONTRIBUTING.md` | How to contribute content (digests, articles, resources) | Contribution workflows or content guidelines change |
| `DEVELOPMENT.md` | Slim redirect to `docs/dev/INDEX.md` | Almost never -- only if docs/dev/ moves |

### User-facing documentation (`docs/`)

Project-specific docs aimed at non-developer users and contributors. Currently none exist; the convention is documented here for future use.

| Convention | Rule |
|------------|------|
| Location | `docs/` (not root, not `docs/dev/`) |
| Naming | UPPERCASE with underscores (e.g., `GETTING_STARTED.md`) |

### Developer documentation (`docs/dev/`)

| File | Purpose | Update when... |
|------|---------|----------------|
| `INDEX.md` | Central index linking to all dev docs | A new dev doc is added or an existing one is renamed/removed |
| `development.md` | Setup, project structure, mailing list, tech stack | Dependencies, project structure, commands, or config change |
| `git-workflow.md` | Commit conventions, branches, PRs, publishing | Git workflow conventions change |
| `design.md` | Website goals and roadmap | Project goals or roadmap change |
| `technology.md` | Technology choices, architecture, phased roadmap | Technology decisions or architecture change |
| `mailing-list-troubleshooting.md` | Common mailing list issues and solutions | New mailing list issues are discovered or resolved |

### Adding new documentation

**User-facing doc** (aimed at users/contributors):

1. Create the file in `docs/` (UPPERCASE, underscores, `.md`)
2. Add a link from `README.md` or `CONTRIBUTING.md` where relevant

**Developer doc** (aimed at developers of the site):

1. Create the file in `docs/dev/` (lowercase, hyphens, `.md`)
2. Add an entry to the table in `docs/dev/INDEX.md`
3. If it replaces content from another doc, remove the old content and add a cross-reference

## What to Update After a Feature

After implementing a feature or significant change, review this checklist:

```
- [ ] Does `README.md` still accurately describe the project and its sections?
- [ ] Does `CONTRIBUTING.md` still reflect the correct contribution process?
- [ ] Does `docs/dev/development.md` still match the project structure, commands, and config?
- [ ] If a new dev topic was introduced, does it need its own doc in `docs/dev/`?
- [ ] Are all cross-references between docs still valid (no broken links)?
- [ ] Do related skills in `.cursor/skills/` need updating?
```

Only update what actually changed -- don't rewrite docs unnecessarily.

## Cross-Reference Rules

- `README.md` and `CONTRIBUTING.md` link to `docs/dev/INDEX.md` for technical details (not directly to sub-docs)
- User-facing docs in `docs/` are linked from `README.md` or `CONTRIBUTING.md` where relevant
- Docs within `docs/dev/` link to each other by relative path (e.g., `[Git Workflow](git-workflow.md)`)
- Docs linking back to root files use `../../` (e.g., `../../README.md`)
- Skills in `.cursor/skills/` reference docs via `../../../docs/dev/` or `../../../docs/` relative paths

## Style Conventions

- Use **sentence case** for headings (capitalize first word only, plus proper nouns)
- Keep docs factual and concise -- no filler
- Use tables for structured reference, bullet lists for quick overviews
- Include code blocks with actual commands when documenting workflows
- Provide good/bad examples when documenting conventions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/roo4l) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
