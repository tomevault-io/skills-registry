---
name: collab
description: | Use when this capability is needed.
metadata:
  author: teslasoft-de
---

# Collaboration State Management

Track collaboration state between Claude sessions using git branches. Enables seamless context switching between conversations by persisting progress in feature branches.

## When to Use

- Starting multi-session work that needs tracking
- Saving progress during long coding sessions
- Handing off work to another conversation
- Resuming previous collaboration branches
- Publishing completed work to development (or master)
- Promoting validated development branch to master

## When NOT to Use

- Simple one-off tasks that don't need tracking
- Work already on master without branching
- Non-git repositories

---

## Quick Start

1. **Start:** `/collab start project/P16/plan` - Create feature branch
2. **Work:** Make changes, use `/collab save "message"` periodically
3. **Handoff:** `/collab checkpoint "message"` - Commit + push for later
4. **Resume:** `/collab resume project/P16/plan` - Continue in new session
5. **Publish:** `/collab publish` - Merge to development (default) or master
6. **Promote:** `/collab promote` - Merge development to master (after E2E validation)

---

## Two-Stage Workflow

Feature branches merge into `development` first, then promote to `master` after E2E validation:

```
feature/P3/parser ──► development ──► master
                   publish          promote
                 (default)       (after E2E)
```

- **`/collab publish`** merges feature → `development` (default target)
- **`/collab promote`** merges `development` → `master` (after validation)
- Use `--target master` with publish to skip the development stage (for repos without a development branch)

### Merge Gate Validation (MRG-001)

When merging to protected branches (master, main, development), tracked git hooks enforce validation:

1. **pre-merge-commit** — Runs tests at the target branch's tier before the merge commit is created
   - Tier 3 (master): `pnpm test:all`
   - Tier 2 (project/*): project-specific tests
   - Blocks merge on failure (bypass: `--no-verify`)
2. **post-merge** — Auto-rebuilds after merge on protected branches
   - Runs: `pnpm build` → `pnpm drone:restart` → `pnpm verify`
   - Never blocks (merge already completed)
   - Reports `POST_MERGE_REDEPLOY` or `POST_MERGE_BUILD_FAIL` to Jarvis

**Two merge paths:**
- **Proper merge** (recommended): Creates merge commit, triggers both hooks, full validation
- **Fast-forward**: No merge commit, hooks never fire, manual `pnpm redeploy` required

Install hooks: `pnpm hooks:install` (symlinks `coordination/git-hooks/` into `.git/hooks/`)

---

## Commands

| Command | Action |
|---------|--------|
| `/collab` | Show current status |
| `/collab start <branch>` | Create new collaboration branch |
| `/collab save "message"` | Commit progress locally |
| `/collab checkpoint "message"` | Commit + push for handoff |
| `/collab publish` | Push to target branch (default: `development`), cleanup |
| `/collab publish --target master` | Push directly to master (skip development) |
| `/collab promote` | Merge `development` → `master` (after E2E validation) |
| `/collab resume <branch>` | Resume existing branch |
| `/collab list` | List all collaboration branches |
| `/collab abandon` | Delete current feature branch (with confirmation) |

---

## Branch Naming

**Vaults:** `<scope>/<id>/<segment>` (e.g., `project/P16/plan`, `goal/Q1-2026/review`)

**Code repos:** `<type>/<id>/<description>` (e.g., `feature/P3/parser`, `bug/P1/fix`)

See [Branch Naming Reference](references/branch-naming.md) for full conventions.

---

## Commit Formats

- **Vaults:** `[collab:<branch>] <message>` (tag in title)
- **Code repos:** Conventional Commits with `[collab:<branch>]` in body

See [Commit Formats Reference](references/commit-formats.md) for examples.

---

## Skill Integrations

### /teslasoft
- After `/collab resume` in vault, suggest running `/teslasoft`
- During `/collab publish` in vault, automatically updates agenda
- During `/collab promote` in vault, automatically updates agenda

### /repomix
- During `/collab publish` in vault, refreshes `vault-bundle.xml`
- During `/collab promote` in vault, refreshes `vault-bundle.xml`

### /e2e
- On `/story` or `/usecase` segments, suggest `/e2e` workflow
- Run E2E validation on `development` before `/collab promote`

See [Integrations Reference](references/integrations.md) for details.

---

## Failure Modes & Recovery

| Issue | Recovery |
|-------|----------|
| Fast-forward fails | Rebase: `git fetch $REMOTE <target> && git rebase $REMOTE/<target>`, then retry |
| Working directory dirty | Run `/collab save` first or stash changes |
| Branch not found | Check spelling, use `/collab list` to see available branches |
| Agenda update fails | Non-blocking; run `/teslasoft update-agenda` manually |
| Promote from wrong branch | Switch to `development` first, or use `--force` to override |
| Development not pushed | `promote` auto-pushes development before merging to master |

---

## Security & Permissions

- **Required tools:** Bash (git commands), Read, Write
- **Confirmations:** Required for `/collab abandon` (destructive)
- **Safe defaults:**
  - Uses `--force-with-lease` (not `--force`)
  - Publish uses remote-only push (`git push $REMOTE branch:<target>`) - never checks out target locally
  - Default publish target is `development` (not `master`)
  - Fast-forward only - no merge commits

---

## Best Practices

1. Start every significant session with `/collab start`
2. Save frequently with `/collab save`
3. Checkpoint before ending conversations
4. Use descriptive segments (`/plan`, `/story`, `/docs`)
5. Include P# in branch names to link to projects
6. Publish when complete to keep master current

---

## Cowork Sessions (Parallel Workflow)

When using **Claude Desktop (Cowork)** alongside **Claude Code**, work splits across two parallel surfaces:

```
Cowork (Desktop)                    Claude Code (Vault)
├── High-level organization         ├── Implementation
├── Skill/plugin creation           ├── Thread orchestration
├── Inbox triage                    ├── TDD, debugging
├── Agenda updates                  ├── Multi-agent delegation
└── Memory sync (.memory/)          └── Git branch workflows
```

### Commit Attribution

Cowork sessions use the `[ai:cowork]` tag — no feature branch required:

```
[ai:cowork] feat(vault-boot): cold-start loader for Cowork sessions
[ai:cowork] chore(P3): update agenda and inbox items
```

### Inbox Integration

Inbox items can be created from **both** surfaces:

| Source | Path | Example |
|--------|------|---------|
| Cowork (external) | `00_Inbox/` via Obsidian MCP | Triage, skill creation, agenda items |
| Claude Code (internal) | `00_Inbox/` via file system | Thread briefs, project phases, research |

Both surfaces read the same inbox. Coordination happens through `.memory/CLAUDE.md` (shared context) and `coordination/` files (harness state).

### Working Memory

The `.memory/CLAUDE.md` file provides shared context across Cowork and Code sessions:
- People, terms, project summaries
- Collab tag conventions
- Infrastructure references
- User preferences

---

## Feature Thread Branches

When a project phase requires multiple parallel implementation threads, use a **shared feature branch** where all sub-agents commit collaboratively.

### Branch Convention

- **Feature branch:** `project/P{N}/{phase-slug}` (e.g., `project/P24/phase2-service-manager`)
- **Created from:** `development` (or current integration branch)
- **Merged back to:** `development` when phase is complete

### Commit Attribution

Sub-agents use `[ai:role@branch]` tags to attribute their work:

```
[ai:developer@project/P24/phase2-service-manager] feat(p24): T1 — ServiceManager core
[ai:doc-specialist@project/P24/phase2-service-manager] docs(p24): D1 — skill doc updates
```

The orchestrator uses `[collab:branch]` tags:

```
[collab:project/P24/phase2-service-manager] chore(p24): Wave 0 — branch setup
```

### Wave/Thread Pattern

Threads are organized into sequential **waves** of parallel work:

| Wave | Threads | Gate |
|------|---------|------|
| Wave 0 | Setup (orchestrator) | Branch + tracking files created |
| Wave 1 | T1, T2 (parallel) | Core implementation verified |
| Wave 2 | T3, T4 (parallel) | Integration verified |
| Wave N | Close (orchestrator) | Full regression green |

Each thread has a verification command that must pass before committing.

---

## References

- [Branch Naming](references/branch-naming.md) - Full naming conventions
- [Commit Formats](references/commit-formats.md) - Message format examples
- [Command Handlers](references/command-handlers.md) - Detailed command procedures
- [Dashboard](references/dashboard.md) - Session-end dashboard format
- [Integrations](references/integrations.md) - Skill integrations

---

## Metadata

```yaml
author: Christian Kusmanow / Claude
version: 2.3.0
last_updated: 2026-02-13
change_surface: references/ (command details, examples)
extension_points: references/command-handlers.md (new commands)
```

### Changelog

- **v2.3.0** (2026-02-13): Cowork parallel workflow
  - Added `[ai:cowork]` commit tag for Claude Desktop / Cowork sessions
  - Added "Cowork Sessions" section documenting parallel workflow pattern
  - Documented inbox integration (items from both Cowork and Code surfaces)
  - Added `.memory/CLAUDE.md` working memory as shared context reference
  - Updated commit-formats reference with Collaboration Tags section
- **v2.2.0** (2026-02-08): Two-stage merge workflow
  - Added `/collab promote` command (development → master after E2E validation)
  - `/collab publish` now targets `development` by default (was: `master`)
  - Added `--target` flag to publish for explicit target selection
  - Use `--target master` to preserve v2.1.0 direct-to-master behavior
- **v2.1.0** (2026-02-05): Safe publish procedure
  - Publish now uses remote-only push (`git push origin branch:master`)
  - Never checks out master locally - prevents inconsistent state on failure
  - Local master ref updated via `git fetch origin master:master` after success
  - Updated error messages with safer rebase instructions
- **v2.0.0** (2026-01-23): SDL migration - Progressive disclosure structure
  - Split 900-line monolith into SKILL.md (~150 lines) + 5 references
  - Added YAML frontmatter with triggers and negative_triggers
  - Added "When to Use / When NOT to Use" sections
  - Added failure modes and security sections
- **v1.5.0** (2026-01-23): Auto-refresh repomix bundle on vault publish
- **v1.4.0** (2026-01-23): Auto-update Teslasoft Agenda on vault publish
- **v1.3.0** (2026-01-22): Differentiated commit formats by repository type
- **v1.2.0** (2026-01-22): Added Collaboration Dashboard
- **v1.1.0** (2026-01-22): Added multi-repo support and WSL paths
- **v1.0.0** (2026-01-21): Initial release

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/teslasoft-de) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
