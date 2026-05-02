---
name: generating-conventional-commits
description: Generates conventional commit messages from git diffs. Use when the user asks about writing commit messages, reviewing staged changes, committing code, or needs help with git commits.
metadata:
  author: aasanchez
---

# Generating Conventional Commits

This Skill teaches Claude to generate properly formatted conventional commit
messages that follow the Conventional Commits specification
(<https://www.conventionalcommits.org/>).

## Overview

Conventional Commits is a specification for adding human and machine-readable
meaning to commit messages. The format correlates with Semantic Versioning
(SemVer):

- `feat` → MINOR version bump
- `fix` → PATCH version bump
- `BREAKING CHANGE` or `!` → MAJOR version bump

## Commit Message Structure

```text
type(scope): subject

[optional body]

[optional footer(s)]
```text

### Type (required)

The commit type communicates the intent of the change:

- **feat**: A new feature for the user (not a new feature for build script)
- **fix**: A bug fix for the user (not a fix to a build script)
- **docs**: Documentation only changes (README, comments, JSDoc)
- **style**: Code style changes (formatting, missing semicolons, whitespace)
- **refactor**: Code changes that neither fix bugs nor add features
- **perf**: Code changes that improve performance
- **test**: Adding missing tests or correcting existing tests
- **chore**: Changes to build process, dependencies, or auxiliary tools
- **ci**: Changes to CI configuration files and scripts
- **build**: Changes to build system or external dependencies

### Scope (optional)

A scope provides additional contextual information about the affected area:

- Noun describing a section of the codebase
- Enclosed in parentheses: `feat(parser): add support for arrays`
- Examples: `api`, `ui`, `auth`, `database`, `core`, `utils`

### Subject (required)

The subject is a brief description of the change:

**Rules**:

- Maximum 50 characters (hard limit: 72)
- Use imperative, present tense: "change" not "changed" nor "changes"
- Don't capitalize the first letter (unless it's a proper noun)
- No period (.) at the end
- Be specific and descriptive

**Good examples**:

- `add user authentication`
- `fix memory leak in event handler`
- `update dependencies to latest versions`

**Bad examples**:

- `Added user authentication` (wrong tense)
- `Fix bug` (not specific)
- `Updated the dependencies.` (has period, capitalized)

### Body (optional)

Use the body to explain:

- **What** changed
- **Why** it changed (motivation)
- **Context** for the change

**Rules**:

- Separate from subject with a blank line
- Wrap at 72 characters
- Use multiple paragraphs if needed
- Use bullet points with `-` or `*` for lists
- Explain what and why, not how (code shows how)

### Footer (optional)

Footers provide metadata:

**Issue references**:

- `Closes #123` - Closes an issue
- `Fixes #456` - Fixes a bug report
- `Refs #789` - References an issue
- Can reference multiple: `Closes #123, #456`

**Breaking changes**:

- `BREAKING CHANGE: description of the breaking change`
- Must be in all caps
- Should explain migration path

## Breaking Changes Notation

Breaking changes can be indicated in two ways:

1. **Append `!` before the colon**:

   ```text
   feat(api)!: change authentication response format
   ```

1. **Use footer**:

   ```text
   feat(api): change authentication response format

   BREAKING CHANGE: The /auth endpoint now returns tokens in a nested
   `credentials` object instead of at the root level.
   ```

## Instructions for Generating Commits

When the user asks you to write a commit message or create a commit:

### Step 1: Detect JIRA Ticket from Branch

Run `git branch --show-current` to get the current branch name:

```bash
git branch --show-current
```

Check if the branch name contains a JIRA ticket ID using this pattern:
`[A-Z][A-Z0-9_]{1,9}-\d+`

JIRA ticket format rules:

- Starts with an uppercase letter (A-Z)
- Followed by 1-9 additional characters: uppercase letters (A-Z), digits (0-9),
  or underscores (_)
- Hyphen separator (-)
- Ends with one or more digits (\d+)

Valid examples: `ABC-123`, `PROJECT-456`, `JIRA-1`, `MY_PROJ-789`, `A1B2-99`

Common branch patterns that contain JIRA tickets:

- `ABC-123-feature-description`
- `feature/PROJ-456-add-login`
- `bugfix/FIX-789-correct-validation`
- `JIRA-1-initial-setup`

If a JIRA ticket is detected, save it to prepend to the commit message.

### Step 2: Examine the Changes

Run `git diff --staged` to understand what will be committed:

```bash
git diff --staged
```

If nothing is staged:

- Inform the user
- Suggest using `git add <files>` first
- Optionally show `git status` to see untracked/modified files

### Step 3: Analyze the Changes

Determine:

1. **Primary intent**: What is the main purpose? (feature, fix, refactor, etc.)
2. **Affected area**: Which component or module is impacted? (for scope)
3. **Complexity**: Does this need a body to explain?
4. **Breaking changes**: Does this break existing functionality?
5. **Related issues**: Are there issue numbers to reference?

### Step 4: Choose the Type

Select the most appropriate type:

- If adding new user-facing functionality → `feat`
- If fixing a user-facing bug → `fix`
- If only changing documentation → `docs`
- If only formatting/style → `style`
- If restructuring without changing behavior → `refactor`
- If improving performance → `perf`
- If only changing tests → `test`
- If updating dependencies or tooling → `chore`

**Note**: When multiple types apply, choose the most significant one from the user's perspective.

### Step 5: Determine Scope

Ask yourself:

- What component/module is affected?
- Is there a clear boundary in the codebase?
- Would scope add useful context?

**Skip scope if**:

- Changes affect multiple unrelated areas
- The project is small and scope adds no value
- You're unsure what scope to use

**Common scopes by project type**:

- Web apps: `api`, `ui`, `auth`, `database`, `middleware`
- Libraries: `core`, `utils`, `parser`, `renderer`
- CLI tools: `cli`, `commands`, `config`

### Step 6: Write the Subject

Craft a concise, imperative description:

**Formula**: `[what you're doing] [to what]`

Examples:

- `add password reset functionality`
- `fix null pointer in user profile handler`
- `update React to version 18`
- `refactor authentication middleware`

### Step 7: Add Body (if needed)

Add a body when:

- The change is complex or non-obvious
- You need to explain the motivation
- Multiple files or components are affected
- Future developers might wonder "why?"

**Skip the body for**:

- Simple, self-explanatory changes
- Obvious bug fixes
- Routine updates

### Step 8: Add Footer (if applicable)

Include footer for:

- Issue references: `Closes #123`
- Breaking changes: `BREAKING CHANGE: description`
- Multiple related issues: `Refs #123, #456`

### Step 9: Review and Present

Before presenting:

1. **Check length**: Subject ≤ 50 chars (preferably, not including JIRA ticket)
2. **Check grammar**: Imperative mood, no period
3. **Check clarity**: Is it specific and clear?
4. **Check completeness**: All relevant info included?
5. **JIRA ticket**: If detected, ensure it's ONLY in the subject line prefix

Present the message to the user.

If JIRA ticket was detected in Step 1:

```text
[JIRA-TICKET] type(scope): subject

body (if any)

footer (if any)
```

If no JIRA ticket was detected:

```text
type(scope): subject

body (if any)

footer (if any)
```

**CRITICAL**: The JIRA ticket must ONLY appear at the beginning of the subject
line in square brackets. Do NOT mention the JIRA ticket anywhere else in the
commit message:

- ❌ Do NOT add it to the body
- ❌ Do NOT add it to the footer
- ❌ Do NOT use "Closes JIRA-123" or "Refs JIRA-123"
- ❌ Do NOT mention it in any other way

**Explain your reasoning**:

- Why you chose this type
- Why you included/excluded scope
- If JIRA ticket detected, mention which branch it came from
- Any important considerations

**Ask for confirmation** before executing `git commit`.

### Step 10: Execute Commit (with approval)

Only after user confirms:

If JIRA ticket was detected:

```bash
git commit -m "$(cat <<'EOF'
[JIRA-TICKET] type(scope): subject

body

footer
EOF
)"
```

If no JIRA ticket:

```bash
git commit -m "$(cat <<'EOF'
type(scope): subject

body

footer
EOF
)"
```

Then confirm success with `git log -1` or `git show HEAD`.

## Examples

See [examples.md](./examples.md) for detailed examples covering various scenarios.

## Best Practices

1. **Atomic commits**: One logical change per commit
2. **Present tense**: "add feature" not "added feature"
3. **Imperative mood**: Commands, not descriptions
4. **Focused scope**: Narrow, well-defined changes
5. **Meaningful subjects**: Specific and descriptive
6. **Explanatory bodies**: For complex changes
7. **Referenced issues**: Always link related issues
8. **Breaking changes**: Always document in footer

## Common Pitfalls to Avoid

- Don't use vague subjects like "fix bug" or "update code"
- Don't mix multiple unrelated changes in one commit
- Don't forget to note breaking changes
- Don't use past tense or continuous tense
- Don't exceed 72 characters in subject line
- Don't add periods to subject lines
- Don't capitalize subject line (except proper nouns)

## Additional Resources

- [Conventional Commits Specification](https://www.conventionalcommits.org/)
- [Semantic Versioning](https://semver.org/)
- [How to Write a Git Commit Message](https://chris.beams.io/posts/git-commit/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aasanchez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
