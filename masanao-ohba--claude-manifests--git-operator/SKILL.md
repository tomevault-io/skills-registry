---
name: git-operator
description: Invoke when performing Git operations (commit, branch, push). Provides commit message conventions, logical change grouping rules, and project-specific policy enforcement for safe and consistent version control. Use when this capability is needed.
metadata:
  author: masanao-ohba
---

# Generic Git Operator

A technology-agnostic skill for managing Git operations according to best practices and project-specific conventions.

## Defaults

- commit: user_request_only
- push: user_request_only
- format: conventional

Project-specific git policies can be defined in the project's CLAUDE.md.

## Core Responsibilities

### 1. Commit Message Principles

**Focus on Role and Definition, Not Description**

```yaml
Good Examples:
  # Role-focused (what this commit accomplishes)
  - "Add user authentication"
  - "Fix race condition in data loader"
  - "Refactor database connection pooling"
  - "Remove deprecated API endpoints"

Bad Examples:
  # Descriptive (what you did, not what it accomplishes)
  - "Changed login function to use JWT tokens"  # ❌ Too descriptive
  - "Modified the database file to support pooling"  # ❌ Describes changes, not purpose
  - "Updated API routes because they were old"  # ❌ Explains process, not result
```

### 2. Commit Grouping Strategy

**Logical Grouping by Purpose**

```yaml
Grouping Rules:
  same_purpose:
    description: "Changes that accomplish the same goal belong in one commit"
    examples:
      - "Add validation: validator file + tests + integration"
      - "Fix bug: root cause fix + related edge case fixes"
      - "Refactor: extracted functions + updated call sites"

  separate_concerns:
    description: "Different purposes must be in separate commits"
    examples:
      - "feat: Add feature X" + "test: Add tests for feature X" # ❌ Should be one commit
      - "fix: Bug A" + "fix: Unrelated bug B" # ❌ Should be separate
      - "refactor: Module A" + "refactor: Module B" # ❌ Only combine if same refactoring goal

  atomic_commits:
    description: "Each commit should be independently functional"
    rules:
      - "Commit should not break the build"
      - "Tests should pass after the commit"
      - "Commit should be revertable without side effects"
```

**Conventional Commit Format**

```yaml
Format: "<type>: <subject>"

Types:
  feat:
    description: "New feature or capability"
    examples:
      - "feat: Add password reset functionality"
      - "feat: Add CSV export for reports"

  fix:
    description: "Bug fix"
    examples:
      - "fix: Resolve memory leak in cache"
      - "fix: Correct timezone calculation"

  refactor:
    description: "Code restructuring without behavior change"
    examples:
      - "refactor: Extract validation logic"
      - "refactor: Simplify error handling"

  docs:
    description: "Documentation changes only"
    examples:
      - "docs: Update API documentation"
      - "docs: Add architecture decision record"

  test:
    description: "Test additions or modifications only (when not part of feat/fix)"
    examples:
      - "test: Add edge cases for parser"
      - "test: Improve test coverage for auth module"

  chore:
    description: "Maintenance tasks, dependencies, configuration"
    examples:
      - "chore: Update dependencies"
      - "chore: Configure CI pipeline"

  perf:
    description: "Performance improvements"
    examples:
      - "perf: Optimize database queries"
      - "perf: Add caching layer"

  style:
    description: "Code style changes (formatting, whitespace)"
    examples:
      - "style: Apply code formatter"
      - "style: Fix linting errors"
```

### 3. Commit Message Language

**Follow Project Output Language**

```yaml
Language Policy:
  source: "Project CLAUDE.md or user instructions"

  examples:
    en:
      - "feat: Add user authentication"
      - "fix: Resolve race condition"
      - "refactor: Extract validation logic"

    ja:
      - "feat: ユーザー認証を追加"
      - "fix: 競合状態を解決"
      - "refactor: バリデーションロジックを抽出"

  guidelines:
    - "Use configured output language consistently"
    - "Keep technical terms in original language when appropriate"
    - "Maintain clarity and brevity regardless of language"
```

### 4. Claude Code Signature Policy

**Conditional Signature Inclusion**

```yaml
Signature Policy:
  source: "Project CLAUDE.md or user instructions"

  when_enabled:
    format: |
      commit message body

      🤖 Generated with [Claude Code](https://claude.com/claude-code)

      Co-Authored-By: Claude <noreply@anthropic.com>

    example: |
      feat: Add user authentication

      Implemented JWT-based authentication with refresh tokens.

      🤖 Generated with [Claude Code](https://claude.com/claude-code)

      Co-Authored-By: Claude <noreply@anthropic.com>

  when_disabled:
    format: |
      commit message body

    example: |
      feat: Add user authentication

      Implemented JWT-based authentication with refresh tokens.

  default: false  # If not specified in config, do not add signature
```

### 5. Git Push Policy

**Conditional Push Permission**

```yaml
Push Policy:
  source: "Project CLAUDE.md or user instructions"

  when_allowed:
    - "Execute git push after successful commit"
    - "Confirm push was successful"
    - "Report remote branch status to user"

  when_disallowed:
    - "Do NOT execute git push"
    - "Inform user that commit is local only"
    - "Remind user to push manually if needed"

  default: false  # If not specified in config, do not push
```

### 6. Pre-Commit Workflow

**Standard Commit Process**

```yaml
Commit Workflow:
  step_1_status:
    command: "git status"
    purpose: "Identify all changes (staged, unstaged, untracked)"
    output: "Present to user for confirmation"

  step_2_diff:
    command: "git diff HEAD"
    purpose: "Review actual code changes"
    output: "Analyze for commit message creation"

  step_3_history:
    command: "git log --oneline -10"
    purpose: "Understand project's commit style"
    output: "Match existing conventions"

  step_4_group:
    action: "Group changes by logical purpose"
    rules:
      - "Same goal = one commit"
      - "Different goals = separate commits"
      - "Each commit should be atomic and functional"

  step_5_message:
    action: "Draft commit message(s)"
    rules:
      - "Follow conventional commit format"
      - "Use configured output language"
      - "Focus on role/purpose, not description"
      - "Include Claude signature if requested"

  step_6_stage:
    command: "git add {files}"
    purpose: "Stage files for current commit"
    notes: "May need multiple add commands for grouped commits"

  step_7_commit:
    command: "git commit -m \"$(cat <<'EOF'\\n{message}\\nEOF\\n)\""
    purpose: "Create commit with formatted message"
    notes: "Use HEREDOC for multi-line messages"

  step_8_verify:
    command: "git status"
    purpose: "Confirm commit was created successfully"

  step_9_push:
    condition: "if user explicitly requested push"
    command: "git push"
    purpose: "Push commits to remote repository"
    fallback: "Inform user to push manually"
```

### 7. Commit Quality Checklist

**Pre-Commit Validation**

```yaml
Quality Checklist:
  message:
    - [ ] Follows conventional commit format
    - [ ] Uses correct output language
    - [ ] Focuses on role/purpose, not description
    - [ ] Is clear and concise
    - [ ] Includes signature only if requested

  changes:
    - [ ] All related changes are grouped logically
    - [ ] Unrelated changes are in separate commits
    - [ ] Commit is atomic (can be reverted independently)
    - [ ] Tests pass after commit (if applicable)
    - [ ] Build succeeds after commit

  process:
    - [ ] Reviewed git status before committing
    - [ ] Reviewed git diff to understand changes
    - [ ] Checked git log for convention consistency
    - [ ] Staged only files for current commit
    - [ ] Verified commit with git status after
    - [ ] Pushed only if explicitly requested
```

## Workflow Examples

### Example 1: Feature Implementation with Tests

```yaml
Scenario: "Implemented user authentication with tests"

Analysis:
  changes:
    - backend/auth.py (new authentication module)
    - backend/tests/test_auth.py (tests for authentication)
    - backend/models/user.py (user model updates)
  purpose: "Add complete authentication feature"
  decision: "Single commit - all changes serve same purpose"

Commit Message (language: en, no signature):
  |
  feat: Add user authentication

  Implemented JWT-based authentication with refresh tokens and comprehensive test coverage.

Commands:
  - git add backend/auth.py backend/tests/test_auth.py backend/models/user.py
  - git commit -m "$(cat <<'EOF'
    feat: Add user authentication

    Implemented JWT-based authentication with refresh tokens and comprehensive test coverage.
    EOF
    )"
  - git status
  - (No push - not requested by user)
```

### Example 2: Multiple Unrelated Fixes

```yaml
Scenario: "Fixed two unrelated bugs"

Analysis:
  changes:
    - frontend/components/DatePicker.js (timezone bug)
    - backend/services/email.py (SMTP connection bug)
  purpose: "Two different bug fixes"
  decision: "Two separate commits - different concerns"

Commit 1 (language: ja, with signature):
  |
  fix: DatePickerのタイムゾーン処理を修正

  ユーザーのローカルタイムゾーンを正しく適用するように修正。

  🤖 Generated with [Claude Code](https://claude.com/claude-code)

  Co-Authored-By: Claude <noreply@anthropic.com>

Commit 2:
  |
  fix: SMTP接続のタイムアウト処理を修正

  接続タイムアウト時に適切にリトライするように修正。

  🤖 Generated with [Claude Code](https://claude.com/claude-code)

  Co-Authored-By: Claude <noreply@anthropic.com>

Commands:
  # First commit
  - git add frontend/components/DatePicker.js
  - git commit -m "$(cat <<'EOF'
    fix: DatePickerのタイムゾーン処理を修正

    ユーザーのローカルタイムゾーンを正しく適用するように修正。

    🤖 Generated with [Claude Code](https://claude.com/claude-code)

    Co-Authored-By: Claude <noreply@anthropic.com>
    EOF
    )"

  # Second commit
  - git add backend/services/email.py
  - git commit -m "$(cat <<'EOF'
    fix: SMTP接続のタイムアウト処理を修正

    接続タイムアウト時に適切にリトライするように修正。

    🤖 Generated with [Claude Code](https://claude.com/claude-code)

    Co-Authored-By: Claude <noreply@anthropic.com>
    EOF
    )"

  - git status
  - (No push - not requested by user)
```

### Example 3: Refactoring with Push Enabled

```yaml
Scenario: "Refactored validation logic across multiple files"

Analysis:
  changes:
    - backend/validators/__init__.py (new validator module)
    - backend/validators/email.py (extracted email validation)
    - backend/validators/phone.py (extracted phone validation)
    - backend/services/user.py (updated to use new validators)
    - backend/services/contact.py (updated to use new validators)
  purpose: "Extract and centralize validation logic"
  decision: "Single commit - all changes serve same refactoring goal"

Commit Message (language: en, no signature, push requested):
  |
  refactor: Extract validation logic to dedicated module

  Centralized email and phone validation to improve reusability and maintainability.

Commands:
  - git add backend/validators/ backend/services/user.py backend/services/contact.py
  - git commit -m "$(cat <<'EOF'
    refactor: Extract validation logic to dedicated module

    Centralized email and phone validation to improve reusability and maintainability.
    EOF
    )"
  - git status
  - git push  # Executed because user requested push
```

## Agent Integration

### Agents That Should Load This Skill

```yaml
Recommended Agents:
  code-developer:
    description: "Implements code changes"
    usage: "Loads skill to commit completed implementations"

  quality-reviewer:
    description: "Reviews code quality"
    usage: "Loads skill to commit approved changes after review"

  deliverable-evaluator:
    description: "Evaluates final deliverables"
    usage: "Loads skill to commit validated deliverables"
```

### When to Use This Skill

```yaml
Trigger Conditions:
  user_requests:
    - "commit these changes"
    - "create a commit"
    - "commit with message..."
    - "save these changes to git"

  agent_decisions:
    - "After completing feature implementation"
    - "After fixing bugs"
    - "After refactoring is complete"
    - "After code review approval"
    - "After deliverable validation"

  workflow_events:
    - "End of development cycle"
    - "Before switching branches"
    - "After significant milestone"
```

## Best Practices

1. **Check Project Instructions**: Read CLAUDE.md for project-specific git policies before creating commits
2. **Review Before Commit**: Always run `git status` and `git diff` to understand full scope of changes
3. **Group Logically**: Combine related changes, separate unrelated changes
4. **Atomic Commits**: Each commit should be independently functional and revertable
5. **Clear Messages**: Focus on what the commit accomplishes, not what you did
6. **Verify After Commit**: Always check `git status` after committing to confirm success
7. **Respect Push Policy**: Only push if explicitly requested by user

## Anti-Patterns to Avoid

```yaml
Bad Practices:
  splitting_logical_units:
    example: "Separating implementation and tests for same feature"
    why: "Tests verify the implementation - they belong together"
    correct: "One commit with both implementation and tests"

  combining_unrelated:
    example: "Fixing bug A and bug B in one commit"
    why: "Makes commits harder to revert and understand"
    correct: "Two separate commits, one for each bug"

  descriptive_messages:
    example: "Updated the login function to use new tokens"
    why: "Describes changes, not purpose"
    correct: "Add JWT-based authentication"

  auto_pushing:
    example: "Always running git push after commit"
    why: "User must explicitly request push"
    correct: "Only push when user requests it"
```

## Universal Application

This skill applies to any project using Git, regardless of:
- Programming language
- Framework
- Project size
- Team size
- Development methodology

The skill adapts through configuration rather than modification, ensuring consistent Git practices across all projects.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/masanao-ohba) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
