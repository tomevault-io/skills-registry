---
name: pr-creator
description: Creates GitHub pull requests in draft mode following Conventional Commits format. Use when user requests "create PR", "make pull request", "open PR", or similar. Automatically pushes branch, analyzes changes, generates structured title/body with proper labels, and assigns to creator. Never modifies code or merges branches.
license: MIT
metadata:
  author: Jonathan Delgado <hi@jon.soy> (https://jon.soy)
  version: "1.0"
---

# PR Creator

## Overview

This skill automates the creation of GitHub pull requests in draft mode following Conventional Commits format. It pushes the current branch, analyzes changes, generates a structured PR with proper labels, and assigns it to the creator. The skill never modifies code or merges branches.

## Important Restrictions

Before starting, verify these restrictions:

- ❌ **DO NOT** change branches
- ❌ **DO NOT** merge any branch
- ❌ **DO NOT** close any pull request
- ❌ **DO NOT** make changes to current code (if needed, mention this and stop the process)
- ⚠️ **STOP** if current branch is `main`, `develop`, `master`, or `HEAD` (cannot create PR from these branches)
- ✅ **ALWAYS** create pull request in draft mode
- ✅ **ALWAYS** use English for title and body

## Process

### Step 0: Identify Ticket ID (if exists)

Extract the ticket ID from the current branch name using common patterns:

```bash
# Get current branch name
git rev-parse --abbrev-ref HEAD
```

**Extraction patterns:**

| Branch Format | Ticket ID |
|---------------|-----------|
| `feat/ABC-123/foo-tar-biz` | `ABC-123` |
| `fix/ABCD-123/handle-null-timestamps` | `ABCD-123` |
| `ABC-456-update-user-profile` | `ABC-456` |
| `refactor/no-ticket-name` | `(no ticket)` |

**Extraction rule:**
- Look for pattern: `LETTERS-NUMBERS` (e.g., `ABC-123`, `ABCD-123`, `JIRA-4567`)
- Usually after first `/` or at the beginning of branch
- If not found, ticket is optional in title

### Step 1: Push branch to remote

```bash
git push origin <branch-name>
```

### Step 2: Review differences

```bash
git diff <base-branch>...<branch-name>
```

**Typical base branches:** `main`, `develop`, `master`, or `HEAD`

### Step 3: Create Pull Request in draft mode

```bash
gh pr create --draft \
  --base <base-branch> \
  --head <branch-name> \
  --title "<pull-request-title>" \
  --body "<pull-request-description>" \
  --assignee @me \
  --label <label-by-type>
```

**Important notes:**
- `--assignee @me`: Automatic assignment to creator
- `--label`: Use appropriate label by change type (see Labels section)

## Title Format

Follow Conventional Commits convention:

```
(TICKET-ID) type(scope): brief description
```

### Title Components

| Component | Required | Description | Example |
|-----------|----------|-------------|---------|
| `TICKET-ID` | ❌ Optional | Ticket identifier (e.g., Jira) | `ABCD-123` |
| `type` | ✅ Required | Change type | `fix`, `feat`, `docs` |
| `scope` | ❌ Optional | Modified module or area | `profile`, `auth` |
| `description` | ✅ Required | Brief description of change | `handle null enrollment timestamps` |

### Change Types

- `fix`: Bug fixes
- `feat`: New feature
- `docs`: Documentation changes
- `style`: Format changes (do not affect logic)
- `refactor`: Code refactoring
- `test`: Test additions or corrections
- `chore`: Maintenance tasks

### Title Examples

```
# With ticket extracted from branch
(ABCD-123) fix(profile): handle null enrollment timestamps by converting to OffsetDateTime at mapper level

# With ticket extracted: feat/ABCD-123/add-profile-endpoint
(ABCD-123) feat(profile): add enrollment timestamp to profile response

# Without ticket in branch
feat(auth): implement JWT token refresh mechanism

# With ticket: refactor/JIRA-789/simplify-validation
(JIRA-789) refactor(validation): simplify token validation logic
```

## Body Format (Description)

The PR body should follow this Markdown structure:

### Template

```markdown
## 🚀 Overview

[Brief description of the change made, explaining the purpose and impact on the project. Include details about added features, resolved issues, or implemented improvements.]

## ✨ Key Features

- [Important feature or change 1]
- [Important feature or change 2]
- [New functionality, performance improvement, bug fix, etc.]

## 🛠️ Changes Made

[Detailed description of code changes:]

- **Modified classes/modules:** [List of files or modules]
- **Added/modified methods:** [Brief description]
- **Business logic changes:** [If applicable]
- **Structural changes:** [If applicable]

## ✅ Validation & Testing

- [Test 1: Description and result]
- [Test 2: Description and result]
- **Command executed:** `[command to run tests]`

## 🔗 Related

- [Link to Jira ticket/Issue]
- [Link to related documentation]
- [Link to other related PRs]
```

### Body Notes

- **Overview**: General context and purpose of change
- **Key Features**: Concise list of most relevant changes
- **Changes Made**: Technical implementation details
- **Validation & Testing**: **Always** include the command used to run tests
- **Related**: Optional section for links to related resources

## Labels and Assignment

**ALWAYS include:**
- `--assignee @me`: Automatic assignment
- `--label <type>`: Label corresponding to change type

### Type to Label Mapping

| Type | Label | Description |
|------|-------|-------------|
| `fix` | `bug` | Bug fixes |
| `feat` | `enhancement` or `feature` | New feature |
| `docs` | `documentation` | Documentation changes |
| `refactor` | `refactor` | Code refactoring |
| `test` | `testing` | Tests |
| `chore` | `maintenance` or `chore` | Maintenance tasks |

## Complete Example

```bash
# 1. Push branch
git push origin fix/handle-null-timestamps

# 2. Review differences
git diff main...fix/handle-null-timestamps

# 3. Create PR in draft
gh pr create --draft \
  --base main \
  --head fix/handle-null-timestamps \
  --title "(ABCD-123) fix(profile): handle null enrollment timestamps" \
  --body "## 🚀 Overview

Fixed null pointer exception when enrollment timestamps are null...

## ✨ Key Features

- Added null checks for enrollment timestamps
- Converted timestamps to OffsetDateTime at mapper level

## 🛠️ Changes Made

- **Modified:** ProfileMapper.java
- **Added:** Null safety checks in timestamp conversion

## ✅ Validation & Testing

- Unit tests passing
- **Command executed:** \`mvn test\`

## 🔗 Related

- [ABCD-123](https://jira.example.com/ABCD-123)" \
  --assignee @me \
  --label bug
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jondotsoy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
