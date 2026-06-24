---
name: setup-agent-files
description: Set up or update repository agent instruction files after installing skills. Use when a user wants AGENTS.md, CLAUDE.md, GEMINI.md, Copilot instructions, Cursor rules, or docs/agents/*.md configured according to the skills installed in the repo. Use when this capability is needed.
metadata:
  author: 0xNino
---

# Setup Agent Files

Create the repo guidance files that make installed skills usable across agents. Keep output minimal, explicit, and based on what is actually installed.

This is a prompt-driven setup skill: inspect, propose, ask with structured choices, then edit.

## Interaction rule

Use the current agent's interactive ask/clarification tool whenever it exists. Present short multiple-choice options with a recommended default so the user can click/select instead of typing.

If the current agent has no ask tool, ask the same question in chat with numbered choices and proceed only after the user answers. Do not silently assume answers for write operations.

Ask at most one setup decision at a time.

## 1. Inspect

Find installed project skills first:

- Prefer `npx skills@latest list --json` if available.
- Read `skills-lock.json` if present.
- Scan common project skill dirs only when needed:
  - `.agents/skills/`
  - `.claude/skills/`
  - `.codex/skills/`
  - `.gemini/skills/`
  - `.github/copilot/skills/`
  - `.cursor/skills/`
  - `skills/`

For each installed skill, read only its `SKILL.md` frontmatter first. Read the body only if the setup decision depends on it.

Also inspect existing guidance files:

- `AGENTS.md`
- `CLAUDE.md`
- `GEMINI.md`
- `.github/copilot-instructions.md`
- `.cursor/rules/*.mdc`
- `CONTEXT.md`, `CONTEXT-MAP.md`, `docs/adr/`
- `docs/agents/`

Do not assume missing files should be created.

## 2. Propose

Summarize:

- installed skills found
- existing agent guidance files found
- support docs worth creating or updating

Then ask for confirmation before writing. Use structured choices for the target agent files, for example:

- Existing agent files only (recommended when any exist)
- `AGENTS.md` only (recommended when none exist)
- `AGENTS.md` plus agent-specific files

If multiple agent files are possible, recommend:

- Update existing agent files first.
- If no agent file exists, create `AGENTS.md` as the shared default.
- Create agent-specific files only when the repo already uses that agent or the user asks for them.

## 3. Agent instruction block

Add or update one block in each selected agent file. Preserve surrounding user content.

Use this shape:

```markdown
## Agent skills

Installed skills live in the project skill directories. Before using a skill, read its `SKILL.md` and only load supporting files when needed.

### Installed skills

- `<skill-name>` — <one-line use case from frontmatter>

### Supporting docs

- `docs/agents/<file>.md` — <why this exists>
```

If the file already has an `## Agent skills` section, replace only that section.

## 4. Support docs by installed skill

Create only docs that are useful for the installed skills.

### Issue workflow docs

If any of these are installed: `to-issues`, `to-prd`, `triage`, `setup-agent-skills`, create or update:

- `docs/agents/issue-tracker.md`
- `docs/agents/triage-labels.md` when `triage` is installed

Ask where issues live with structured choices:

- GitHub Issues (recommended when a GitHub remote exists)
- GitLab Issues (recommended when a GitLab remote exists)
- Local markdown
- Other

Use existing remotes as evidence, not as final authority.

### Domain docs

If any of these are installed: `diagnose`, `tdd`, `grill-with-docs`, `improve-codebase-architecture`, `zoom-out`, create or update:

- `docs/agents/domain.md`

Ask whether to create domain docs with structured choices:

- Use existing `CONTEXT.md` / ADRs only
- Create or update `docs/agents/domain.md`
- Skip domain setup

Record how agents should consume `CONTEXT.md`, `CONTEXT-MAP.md`, and ADRs. Do not create domain docs unless the user confirms.

### Review docs

If any of these are installed: `review-swarm`, `review-and-simplify-changes`, create or update:

- `docs/agents/review.md`

Record review scope defaults, severity expectations, and whether review agents may edit files.

### Visual docs

If `visual-explainer` is installed, create or update:

- `docs/agents/visuals.md`

Record preferred output directory, whether generated HTML should be committed, and any sharing/deployment constraints.

### Repository reference docs

If `librarian` is installed, create or update:

- `docs/agents/repositories.md`

Record cache location preferences and rules for using remote repositories as references.

## 5. Keep it lean

Do not create docs for skills that do not need repo-specific configuration. Interaction/productivity skills such as `caveman`, `handoff`, `grill-me`, and `write-a-skill` usually only need to be listed in the agent instruction block.

Do not add local development notes, private setup details, or generated session context. If the repo has a private ignored directory for local notes, leave it ignored and do not reference its contents.

## 6. Done

Report:

- files created or updated
- installed skills covered
- any skills that need no repo-specific docs
- any decisions the user deferred

---
> Source: [0xNino/skills](https://github.com/0xNino/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
