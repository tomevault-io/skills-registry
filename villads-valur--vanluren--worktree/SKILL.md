---
name: worktree
description: Create a git worktree for a new workstream, with a relevant issue/ticket name and title Use when this capability is needed.
metadata:
  author: villads-valur
---

# Git Worktree

Create a git worktree for a workstream, add a `.claude/` directory in the worktree with `planning.md` and `decisions-and-learnings.md`.

## Templates

Templates are bundled with this skill:
- [planning-template.md](planning-template.md) - for `.claude/planning.md`
- [decisions-template.md](decisions-template.md) - for `.claude/decisions-and-learnings.md`

## Naming

- Use the `{project-name}-{issue-id}-{description}` naming structure
- Only add the issue id from Linear or GitHub if available
- Description should be kebab-case and concise (2-4 words)

### Examples

- legora-ENG-123-fix-recent-files-dnd
- arcane-456-fix-auth-sessions
- buzzwork-add-organization-multi-tenancy

## Process

1. **Check for existing worktrees**

   ```bash
   git worktree list
   ```

   - If a similar worktree exists, ask for directions before proceeding

2. **Determine project name**

   - Extract from the main worktree directory name (e.g., `myapp-main` → `myapp`)

3. **Create new worktree**

   ```bash
   git -C {project}-main worktree add ../{project}-{issue-id}-{description} -b {issue-id}/{description}
   ```

4. **Switch to worktree directory**

   - All subsequent file operations happen in `{worktree}/`, not the parent container

5. **Create `.claude/` directory**

   ```bash
   mkdir -p {worktree}/.claude
   ```

6. **Create planning.md**

   - Use template from `${CLAUDE_SKILL_DIR}/planning-template.md`
   - Fill in the task description and initial plan based on the issue/ticket
   - Update the date to today

7. **Create decisions-and-learnings.md**

   - Use template from `${CLAUDE_SKILL_DIR}/decisions-template.md`
   - Update the date to today

8. **Read issue/ticket** (if available)

   - If a Linear or GitHub issue URL/ID was provided, fetch and read it
   - Use the issue details to populate the planning.md

9. **Confirm completion**

   - Report the worktree location and branch name
   - Ask if I want to start working on the task

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/villads-valur) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
