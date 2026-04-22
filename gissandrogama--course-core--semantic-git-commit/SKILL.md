---
name: semantic-git-commit
description: Automates the creation of semantic git commits by staging changes, analyzing diffs, and generating structured, descriptive commit messages in English. Use when the user asks to "commit changes", "create a commit", or "wrap up work".
metadata:
  author: gissandrogama
---

# Semantic Git Commit

This skill guides the agent through the process of creating a high-quality, semantic git commit based on the current workspace changes.

## Workflow

Follow these steps strictly to ensure consistency and quality.

### 1. Stage Changes

Always start by staging all current changes to ensure the commit captures the full context of the work.

```bash
git add .
```

### 2. Analyze Changes

Retrieve the list of staged files and their status to understand the scope of the commit.

```bash
git diff --cached --name-status
```

If necessary (e.g., for detailed logic changes), inspect the actual diff content for key files:

```bash
git diff --cached
```

### 3. Generate Commit Message

Construct a semantic commit message following the **Conventional Commits** specification.

**Rules for Commit Message:**
*   **Language:** English.
*   **Structure:**
    *   **Header:** `type(scope): Short description` (e.g., `feat(pix_payments): Implement scheduled payments`).
    *   **Body:** Bullet points (`- `) listing specific changes.
*   **Content:**
    *   Explicitly list modified modules and functions (e.g., `Bookkeeper.PIXPayments.create/2`).
    *   Explain *what* changed and *why* (succinctly).
    *   Do NOT use Markdown formatting (no bold `**`, no code backticks `` ` ``) in the message itself.

**Template:**
```text
type(scope): Description of the primary change

- Add function Name/Arity in ModuleName
- Update logic in ModuleName to handle X
- Remove validation Y from ModuleName
- Add/Update tests in TestModuleName
```

**Types:**
*   `feat`: New feature.
*   `fix`: Bug fix.
*   `refactor`: Code change that neither fixes a bug nor adds a feature.
*   `chore`: Maintenance, config, build, etc.
*   `docs`: Documentation only.
*   `test`: Adding missing tests or correcting existing tests.

### 4. Execute Commit

Apply the commit using the generated message.

```bash
git commit -m "generated message"
```

### 5. Verify

Confirm the commit was successful.

```bash
git status
```

### 6. Push Changes

After committing, check if the current branch has an upstream remote branch.

```bash
git rev-parse --abbrev-ref --symbolic-full-name @{u}
```

- **If the command returns an error** (meaning no upstream is set), push and set the upstream:

  ```bash
  # Get current branch name
  BRANCH=$(git rev-parse --abbrev-ref HEAD)
  # Push to origin
  git push --set-upstream origin $BRANCH
  ```

- **If the command returns a branch name**, simply push:

  ```bash
  git push
  ```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gissandrogama) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
