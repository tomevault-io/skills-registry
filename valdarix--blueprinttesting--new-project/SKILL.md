---
name: new-project
description: Bootstrap a new project end-to-end. Use when the user wants to start a new project of any type — web app, mobile app, desktop app, game, game mod, CLI tool, library/package, browser extension, API/backend service, or AI/ML project. Interviews the user to understand requirements, researches the chosen stack, generates comprehensive documentation (PROJECT.md, ARCHITECTURE.md, STACK.md, ROADMAP.md, CONVENTIONS.md, DECISIONS.md, TEAM.md), scaffolds the codebase with proper configs and dependencies, creates specialized Claude Code agents and path-scoped rules, and assembles a persistent development team ready to begin work. Trigger when user mentions starting a new project, bootstrapping, scaffolding, initializing a codebase, or setting up a development environment from scratch. Use when this capability is needed.
metadata:
  author: valdarix
---

# New Project Bootstrap

Bootstrap a new project through five phases: Discover, Document, Build, Launch, Shutdown. Each phase builds on the previous — do not skip phases or reorder them.

## Phase 1: Discover

### 1A: Interview

Conduct 4 rounds of progressive questioning. Accept shorthand answers (e.g., "next" = Next.js, "ts" = TypeScript, "pg" = PostgreSQL). Do not overwhelm — ask one round at a time, wait for answers before proceeding.

**Round 1: Project Type** (ask directly, no reference file needed)

Present these options:
1. Web Application (SPA, SSR/full-stack, static site, e-commerce)
2. Mobile App (native iOS, native Android, cross-platform)
3. Desktop Application (Electron, Tauri, native)
4. Game (2D, 3D, web-based)
5. Game Mod (Unity, Godot, Unreal, Source, Minecraft/Java)
6. CLI Tool (simple utility, complex multi-command, TUI)
7. Library/Package (npm, PyPI, crates.io, Go module)
8. Browser Extension (Chrome, Firefox, cross-browser)
9. API/Backend Service (REST, GraphQL, real-time/WebSocket)
10. AI/ML Project (model training, inference service, data pipeline, AI agent)

Also ask for the project name and a one-sentence description.

Record the project type, subtype, name, and description for DECISIONS.md.

**Rounds 2-4: Stack, Scope, Preferences**

Read the relevant section from `references/interview-trees.md` based on the chosen project type. Ask questions from:
- Round 2: Stack & Framework choices
- Round 3: Scope & Features (MVP definition)
- Round 4: Preferences & Constraints (deployment, team, timeline, design)

After each answer, record the decision and rationale for DECISIONS.md.

### 1B: Research

After the interview, research the chosen stack. Read the relevant section from `references/stack-profiles.md` for research prompts.

**Context7 research:** For each major dependency in the chosen stack:
1. Call `resolve-library-id` to find the library
2. Call `query-docs` with the specific query from stack-profiles.md
3. Note key patterns, conventions, and best practices

**Web research:** Run the WebSearch prompts from stack-profiles.md (substitute `{{YEAR}}` with the current year). Focus on:
- Production best practices for the chosen stack
- Common pitfalls and how to avoid them
- Recommended project structure

Compile research findings — these inform documentation and scaffold decisions.

## Phase 2: Document

Read `references/doc-templates.md` for all templates.

Create 7 files in `docs/` plus update the CLAUDE.md files. Draft each document using information from Phase 1 (interview answers + research findings). Fill in all template placeholders with project-specific content.

### Document creation order:

1. **docs/PROJECT.md** — Project overview, problem statement, target users, key features
2. **docs/STACK.md** — Technology table, framework details, infrastructure, dev tools
3. **docs/ARCHITECTURE.md** — System overview, directory structure, modules, data flow, design patterns
4. **docs/CONVENTIONS.md** — Code style, git workflow, component patterns, testing, error handling
5. **docs/DECISIONS.md** — Pre-populate with all decisions captured during Phase 1 interview
6. **docs/ROADMAP.md** — Vision, 3 phases with task checklists, future considerations
7. **docs/TEAM.md** — Team roster (filled in Phase 4), communication patterns, PR workflow

### CLAUDE.md updates:

Update the root `CLAUDE.md` with:
- Project summary (1-2 sentences)
- Stack summary (key technologies)
- Convention pointers (reference docs/CONVENTIONS.md)
- Available skills list
- Quick reference commands (dev server, test, build, lint)

Update `.claude/CLAUDE.md` with:
- Agent coordination info
- Doc references (paths to all docs/ files)
- Available skills and when to use them
- Team workflow notes

For each document, draft the full content in one pass, then review for consistency with other docs. Use the section-by-section approach from doc-coauthoring when a section needs user input — but most content should be derivable from Phase 1 answers.

## Phase 3: Build

### 3A: Scaffold

1. Run `scripts/init-project.sh --name "{{PROJECT_NAME}}"` to create the universal skeleton
2. Read the relevant stack profile from `references/stack-profiles.md` for init commands
3. Execute the stack-specific init commands (e.g., `npx create-next-app`, `cargo init`)
4. Install core packages from the stack profile
5. Install optional packages based on features chosen in Phase 1
6. Create configuration files (linter, formatter, tsconfig, etc.)
7. Create `.env.example` with all required environment variables documented
8. Update `.gitignore` with stack-specific entries from the stack profile
9. Create initial git commit: `git add -A && git commit -m "chore: initial project scaffold"`

If the project directory already has files (e.g., from the boilerplate), integrate the scaffold around existing files rather than overwriting them.

### 3B: Generate Agents

Read `references/agent-blueprints.md` for templates.

**Always create these universal agents:**
- `code-reviewer.md` — Opus, plan mode, read-only tools, PR review specialist
- `project-lead.md` — Opus, default mode, full tools, project coordinator

**Then create 3-5 type-specific agents** based on the project type. Use the selection guide in agent-blueprints.md to determine which specialists to create.

Write each agent to `.claude/agents/{{agent-name}}.md` with:
- YAML frontmatter (name, model, description, mode, tools)
- System prompt referencing specific docs/ files by path
- Responsibilities section tailored to this project
- Workflow section with branch/PR conventions
- Constraints section with guardrails

All agents must be instructed on the GitHub issue-driven workflow (using the `gh-cli` skill for all GitHub operations):
1. Claim a GitHub issue (`gh issue edit N --add-assignee @me`)
2. Create a branch from the issue (`gh issue develop N --branch type/issue-N-description`)
3. Work in plan mode — submit plan to team lead for approval before implementing
4. Implement changes following project conventions
5. Write/update tests
6. Push and create a PR linking to the issue (`gh pr create --title "type: description" --body "Closes #N"`)
7. Code reviewer reviews the PR (`gh pr review`)
8. Address review feedback, then merge (`gh pr merge --squash --delete-branch`)

**All type-specific agents must use `mode: plan`** (require plan approval). Only the project-lead uses default mode. When a teammate finishes planning, the team lead reviews and approves or rejects with feedback before the teammate can implement.

### 3C: Generate Rules

Read `references/rules-blueprints.md` for templates.

**Always create these universal rules:**
- `.claude/rules/shared/git-workflow.md` — Branch naming, commit format, PR process
- `.claude/rules/shared/code-style.md` — Formatting, naming, file organization
- `.claude/rules/shared/documentation.md` — When to update docs, comment style

**Then create framework-specific rules** based on the chosen stack. Use the selection guide in rules-blueprints.md. Write rules to `.claude/rules/{{layer}}/{{rule-name}}.md` with appropriate `globs` frontmatter for path scoping.

Organize rules by layer:
- `shared/` — Apply everywhere
- `frontend/` — Frontend-specific
- `backend/` — Backend-specific
- `testing/` — Test file conventions

### 3D: Generate Hooks

Read `references/hooks-blueprints.md` for templates.

Set up context rot mitigation hooks that prevent degradation during long-running team sessions:

1. Create `.claude/hooks/` directory
2. Create `.claude/hooks/session-start.js` from the SessionStart hook template
3. Create `.claude/hooks/pre-compact.js` from the PreCompact hook template
4. Create or merge into `.claude/settings.json` with:
   - `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=70` environment variable
   - Hook config pointing to both hook scripts
5. Add the Context Recovery block from hooks-blueprints.md to `.claude/CLAUDE.md`
6. Verify hooks execute without errors: `node .claude/hooks/session-start.js <<< '{"source":"startup"}'` and `node .claude/hooks/pre-compact.js <<< '{}'`

If `.claude/settings.json` already exists, merge the new keys into the existing file rather than overwriting it. If `.claude/settings.local.json` exists, do not modify it — it contains user-specific overrides.

## Phase 4: Launch

### 4A: Create Team

Use `TeamCreate` to create a development team for the project.

### 4B: Create GitHub Issues from Roadmap

Read `docs/ROADMAP.md` Phase 1 items. For each task:
1. Create a GitHub issue using `gh issue create --title "type: description" --labels "phase-1,area" --body "..."` with acceptance criteria in the body
2. Also create a local task with `TaskCreate` that references the GitHub issue number
3. Set appropriate dependencies between tasks with `TaskUpdate`

This ensures every piece of work is tracked as a GitHub issue — the source of truth for project progress.

### 4C: Spawn Team Members

Spawn team members from the agent definitions created in Phase 3B. **All type-specific agents must be spawned with plan mode required** so the team lead must approve their implementation plans before they write code.

1. Spawn the `code-reviewer` agent first (needed for PR reviews)
2. Spawn the `project-lead` agent (default mode — coordinates and approves plans)
3. Spawn type-specific agents with `mode: plan` as needed for Phase 1 tasks

Brief each team member with:
- Project name and description
- Their role and key responsibilities
- Paths to relevant docs/ files they should read
- The GitHub issue-driven workflow: claim issue → branch → plan (get approval) → implement → PR → review → merge
- That they must use `gh` CLI for all GitHub operations (issues, PRs, reviews)
- **Subagent delegation pattern:** Always spawn Task subagents for implementation work — keep your own context clean for coordination, planning, and communication. See docs/TEAM.md for full details.
- **Context management:** Hooks are configured for automatic context recovery (70% autocompact threshold, SessionStart hook restores state after compaction). If responses degrade, run `/compact` proactively.

### 4D: Assign Work and Wait

Use `TaskUpdate` to assign initial tasks to appropriate team members based on their specialization. Tell teammates which GitHub issue to claim.

**Important: Wait for teammates to complete their tasks before proceeding.** Do not start implementing tasks yourself — use delegate mode to focus on coordination. The project-lead should:
- Monitor teammate progress
- Review and approve/reject teammate plans when they submit them
- Ensure code-reviewer reviews every PR before merge
- Only proceed to the next phase when all current phase tasks are complete
- Reassign work if a teammate gets stuck

## Phase 5: Shutdown

Gracefully shut down the team after all work is complete. **TeamDelete will fail if teammates are still active** — the shutdown handshake ensures it succeeds.

### 5A: Shutdown Teammates

For each teammate:
1. Send a `shutdown_request` via `SendMessage` with `type: "shutdown_request"`
2. Wait for the teammate to respond with `shutdown_response` and `approve: true`
3. If a teammate rejects, check why — they may have unfinished work. Resolve the blocker, then retry.

Shut down implementer agents first, then the code-reviewer, then the project-lead last.

### 5B: Clean Up Team

After **all** teammates have acknowledged shutdown:
1. Call `TeamDelete` to remove the team and its task directory
2. This cleans up `~/.claude/teams/{{team-name}}/` and `~/.claude/tasks/{{team-name}}/`

### 5C: Final Updates

1. Update `docs/ROADMAP.md` — check off completed tasks, note any deferred work
2. Create a summary commit: `git add -A && git commit -m "chore: complete project bootstrap"`
3. Report a summary to the user: what was built, how many issues created, team composition, and next steps

## Reference Files

| File | Load When | Purpose |
|------|-----------|---------|
| [references/interview-trees.md](references/interview-trees.md) | Phase 1A, after Round 1 | Drill-down questions per project type |
| [references/stack-profiles.md](references/stack-profiles.md) | Phase 1B + 3A | Stack init commands, packages, research queries |
| [references/doc-templates.md](references/doc-templates.md) | Phase 2 | Templates for all docs/ files |
| [references/agent-blueprints.md](references/agent-blueprints.md) | Phase 3B | Agent definition templates |
| [references/rules-blueprints.md](references/rules-blueprints.md) | Phase 3C | Rule templates by stack/layer |
| [references/hooks-blueprints.md](references/hooks-blueprints.md) | Phase 3D | Context rot mitigation hooks and settings |

## Integration with Existing Skills

- **gh-cli** — Used by ALL agents for GitHub operations (issues, PRs, reviews, branches). Core to the issue-driven development workflow. Every agent system prompt should reference this skill.
- **doc-coauthoring** — Available for future doc revisions. Reference in generated CLAUDE.md.
- **frontend-design** — Add to skills list for frontend-architect agent on web projects.
- **web-artifacts-builder** — Available for frontend agents doing React/Tailwind/shadcn prototyping.
- **skill-creator** — Reference in generated CLAUDE.md for creating project-specific skills later.

## Error Handling

- If a stack init command fails, check the error, try to resolve (missing dependency, wrong Node version), and retry once. If still failing, inform the user and continue with manual setup.
- If Context7 returns no results for a library, fall back to WebSearch for documentation.
- If the project directory has existing files that conflict with scaffold, ask the user how to proceed rather than overwriting.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valdarix) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
