---
name: ralphy
description: Autonomous AI coding loop. Default: fast setup from minimal input. Use --detailed for iterative PRD refinement. Triggers on: ralphy, run ralphy, start ralphy, autonomous implementation. Use when this capability is needed.
metadata:
  author: ondrejdrapalik
---

# Ralphy - Autonomous AI Coding Loop

A unified skill for setting up autonomous feature implementation.

**You do NOT run the script. You help the user set up and create the PRD, then they run it.**

**IMPORTANT: Worktree-first approach.** All files go into `.ralphy/` inside the worktree. Nothing gets written to the main branch.

---

## Two Modes

### Default Mode (Fast)
Just create the worktree and PRD immediately from the user's feature description. No questions asked.

**Trigger:** `/ralphy <feature description>`

### Detailed Mode (Iterative PRD)
Ask clarifying questions and iterate on the PRD with user feedback.

**Trigger:** Any of these:
- `/ralphy --detailed <feature description>`
- User says "iterate on PRD", "detailed PRD", "refine the PRD"
- User explicitly asks for clarifying questions

---

## Step 1: Determine Mode

**If DEFAULT mode:** Skip to Step 2 immediately.

**If DETAILED mode:** Ask clarifying questions first:

### Clarifying Questions Format

```
1. What is the primary goal of this feature?
   A. [Option A]
   B. [Option B]
   C. [Option C]
   D. Other: [please specify]

2. Who is the target user?
   A. New users only
   B. Existing users only
   C. All users
   D. Admin users only
```

Users respond with "1A, 2C, 3B" for quick iteration. Continue asking follow-up questions until PRD scope is clear.

---

## Step 2: Create Worktree Using /wt.create

**Create the worktree immediately** (default mode) or after user answers questions (detailed mode).

Invoke the `/wt.create` skill with the feature description:
```
/wt.create <feature description>
```

This will:
- Create `_wt/wtN_<slug>` worktree with numbered prefix
- Create matching branch name
- Add `_wt/` to `.gitignore`
- Copy `.env` files to worktree
- Create tmux window
- Copy path to clipboard

**Save the worktree path** returned by wt.create for Step 3.

---

## Step 3: Setup Files in Worktree

After `/wt.create` completes, create `.ralphy/` and copy files into it:

```bash
# Get worktree path from wt.create output (e.g., _wt/wt1_feature-name)
WT_PATH="<path-from-wt-create>"

# Create .ralphy directory
mkdir -p "${WT_PATH}/.ralphy"

# Copy ralphy.sh into .ralphy/
cp ~/.claude/skills/ralphy/ralphy.sh "${WT_PATH}/.ralphy/ralphy.sh"
chmod +x "${WT_PATH}/.ralphy/ralphy.sh"

# Create progress.txt in .ralphy/
touch "${WT_PATH}/.ralphy/progress.txt"

# PRD.md will be written to .ralphy/ in Step 4
```

---

## Step 4: Generate PRD in .ralphy/

Create the PRD **inside `.ralphy/`**.

**CRITICAL: NEVER use the Write tool for worktree files.** The Write tool resolves paths relative to CWD (main repo), not the worktree. Always use `cat > ... << 'EOF'` via Bash to write files into the worktree path.

```bash
# Write PRD to .ralphy/ (NOT worktree root, NOT main branch)
cat > "${WT_PATH}/.ralphy/PRD.md" << 'EOF'
# [Feature Name]
...
EOF
```

### PRD Structure

```markdown
# [Feature Name]

[Brief description of the feature and problem it solves]

## Goals

- Goal 1
- Goal 2

## Tasks

- [ ] Task 1 (specific and actionable)
- [ ] Task 2
- [ ] Task 3

## Functional Requirements

- FR-1: The system must...
- FR-2: When a user...

## Non-Goals

- What this will NOT include

## Technical Considerations

- Constraints, dependencies, etc.
```

### Task Size Rule

**Each task must be completable in ONE iteration.**

**Right-sized:**
- Add a database column and migration
- Add a UI component to an existing page
- Update a server action with new logic

**Too big (split these):**
- "Build the dashboard" → Split into: schema, queries, UI, filters
- "Add authentication" → Split into: schema, middleware, login UI, sessions

**Rule of thumb:** If you cannot describe the change in 2-3 sentences, it's too big.

### Task Ordering

Tasks execute in order. Earlier tasks must NOT depend on later ones.

1. Schema/database changes (migrations)
2. Server actions / backend logic
3. UI components
4. Dashboard/aggregation views

### Good vs Bad Task Descriptions

**Good:**
- "Add `status` column to tasks table with default 'pending'"
- "Create filter dropdown with options: All, Active, Completed"

**Bad:**
- "Make it work correctly"
- "Fix the bugs"
- "Improve UX"

---

## Step 5: Tell User Setup is Complete

Tmux window and clipboard were already handled by `/wt.create`.

### Tell user:
```
✅ Setup complete!

Worktree: <wt-name> (tmux window created)
PRD: .ralphy/PRD.md

To run:
  ./.ralphy/ralphy.sh --prd .ralphy/PRD.md --codex

Other engines: ./.ralphy/ralphy.sh --prd .ralphy/PRD.md (Claude)
```

### If user explicitly said "no worktree":

Copy files to `.ralphy/` in project root instead:
```bash
mkdir -p .ralphy
cp ~/.claude/skills/ralphy/ralphy.sh ./.ralphy/ralphy.sh
chmod +x ./.ralphy/ralphy.sh
touch ./.ralphy/progress.txt
# Write PRD.md to .ralphy/
```

Then tell user:
```
✅ Setup complete!

To run Ralphy:
  ./.ralphy/ralphy.sh --prd .ralphy/PRD.md              # Claude Code (default)
  ./.ralphy/ralphy.sh --prd .ralphy/PRD.md --codex      # Codex CLI
  ./.ralphy/ralphy.sh --prd .ralphy/PRD.md --opencode   # OpenCode
```

**DO NOT run the script yourself. The user runs it.**

---

## After Ralphy Completes

When the autonomous loop finishes:

1. **Review changes** in the worktree
2. **Apply to main** using `/wt.apply` (rebases and fast-forward merges)
3. **Clean up** using `/wt.remove` (deletes worktree, branch, tmux window)

---

## Checklist

Before finishing:

- [ ] Determined mode (default=fast, or --detailed)
- [ ] If --detailed: Asked clarifying questions with lettered options
- [ ] Used /wt.create for worktree setup (unless user said "no worktree")
- [ ] Created .ralphy/ directory in worktree
- [ ] Copied ralphy.sh into .ralphy/ (not worktree root)
- [ ] Created progress.txt in .ralphy/
- [ ] Created PRD.md with properly sized tasks in .ralphy/
- [ ] Tasks are ordered by dependency (schema → backend → UI)
- [ ] Told user the run command (tmux window already created by wt.create)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ondrejdrapalik) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
