---
name: git-autopilot
description: Automates the full git commit and push workflow in VS Code. Analyzes staged and unstaged changes, generates a Conventional Commits message, stages files, commits, and pushes to the tracked remote or a specified branch. Use this when asked to commit changes, save work, generate a commit message, write a commit, push to GitHub, sync with remote, publish changes, stage and commit specific files, push to a different branch, or push to a feature branch.
metadata:
  author: mickpletcher
---

# Git Commit and Sync

This skill automates the full git workflow: analyzing changes, generating a commit message, staging, committing, and pushing to GitHub.

## When to use this skill

Use this skill when the user asks to:
- Commit changes or "save my work"
- Generate or write a commit message
- Push, sync, or publish to GitHub
- Stage and commit specific files

## Commit message format

All commit messages follow the Conventional Commits standard:

```
type(scope): short description

[optional body for complex changes]
```

**Types:**

| Type | Use when |
|---|---|
| `feat` | Adding a new feature or capability |
| `fix` | Fixing a bug or broken behavior |
| `docs` | Documentation only changes |
| `refactor` | Code restructured without behavior change |
| `perf` | Performance improvement |
| `test` | Adding or updating tests |
| `chore` | Build process, dependency updates, tooling |
| `ci` | CI/CD pipeline changes |
| `style` | Formatting, whitespace, no logic change |

**Scope** is the affected module, file area, or component (for example, `auth`, `pipeline`, `deploy`, `api`). Omit if the change is broad.

**Short description** rules:
- Lowercase, no period at the end
- 72 characters or less
- Imperative mood ("add feature" not "added feature")

**Body** (optional): Include when the change is non-obvious, has side effects, or requires context for reviewers. Wrap at 72 characters.

## Workflow

### Step 1 — Assess current state

Run `git status` to see the current state of the working tree. Identify:
- Which files are staged (ready to commit)
- Which files are modified but not staged
- Which files are untracked

### Step 2 — Review the diff

Run `git diff --staged` to read what is actually staged. If nothing is staged but there are modified files, run `git diff` to review unstaged changes.

Use the diff output to determine:
- What changed (additions, deletions, modifications)
- Which files are affected
- The logical grouping of changes

### Step 3 — Stage files

If the user specified files in their request, stage only those:
```bash
git add <file1> <file2>
```

If the user said "everything" or did not specify, stage all changes:
```bash
git add -A
```

If files are already staged, skip this step.

### Step 4 — Generate commit message

Analyze the staged diff and construct a commit message using the format above.

**Guidelines for generating the message:**
- Summarize what the code does, not what files changed
- If multiple unrelated changes exist, note that in the body or recommend splitting into separate commits
- Prefer specificity: `fix(auth): handle null token on refresh` over `fix: bug fix`
- For new scripts or automation files: `feat(scope): add <name> script to <purpose>`
- For refactors: describe what was restructured, not that it was restructured
- For config changes: name the config and what changed

**Examples:**

```
feat(deploy): add proxmox lxc provisioning script

chore(deps): update pester to v5.6.1

fix(pipeline): correct null check on empty response array

refactor(auth): extract token validation into separate function

docs(api): add endpoint examples for safesend webhook receiver

ci(github-actions): add lint step to pull request workflow
```

### Step 5 — Present message for confirmation

Show the user the generated commit message before executing. Format the output as:

```
Proposed commit message:

  type(scope): short description

  [body if applicable]

Proceed? (yes to commit, or provide changes)
```

If the user confirms, proceed. If they request changes, revise the message and confirm again before committing.

### Step 6 — Commit

```bash
git commit -m "type(scope): short description"
```

For messages with a body, use a multi-line commit:
```bash
git commit -m "type(scope): short description" -m "body text here"
```

### Step 7 — Resolve target branch

Before pushing, determine the target branch using this priority order:

1. Branch explicitly named by the user in their request (e.g. "push to feature/my-branch")
2. Branch passed as an argument (e.g. `--branch feature/my-branch`)
3. The current branch's tracked upstream
4. The current branch name with no upstream set

If the user named a branch that does not exist locally or remotely, confirm before creating it.

### Step 8 — Push

Push to the resolved target branch.

Push to the tracked upstream (default case):
```bash
git push
```

Push to a different remote branch (user specified a target):
```bash
git push origin <target-branch>
```

Push a local branch that has no upstream set:
```bash
git push --set-upstream origin <branch-name>
```

Push the current branch to a different target branch name:
```bash
git push origin HEAD:<target-branch>
```

If the push is rejected due to upstream changes, report this to the user and do not force push. Recommend they pull and resolve conflicts first:
```bash
git pull --rebase
```

## Edge cases

- **Nothing to commit**: Report the clean state and exit. Do not attempt a commit.
- **Merge conflicts present**: Stop immediately and tell the user to resolve conflicts before committing.
- **Detached HEAD state**: Warn the user and do not commit until they are on a named branch.
- **Large number of changed files**: Summarize by directory or module rather than listing every file in the commit body.
- **Mixed unrelated changes**: Flag this to the user and recommend splitting into multiple focused commits before proceeding.
- **Target branch does not exist**: Confirm with the user before creating a new remote branch.
- **Target branch diverged**: Report the divergence and do not force push. Recommend pulling or rebasing the target branch first.
- **Pushing to a protected branch**: Report that the push was rejected and defer to the user. Do not attempt to bypass branch protection.

---
> Source: [mickpletcher/VSCode](https://github.com/mickpletcher/VSCode) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
