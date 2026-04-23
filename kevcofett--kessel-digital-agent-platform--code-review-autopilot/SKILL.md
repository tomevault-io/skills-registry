---
name: code-review-autopilot
description: > Use when this capability is needed.
metadata:
  author: kevcofett
---

# Code Review Autopilot

## Commands

- `/review` — Review all staged/changed files
- `/review <path>` — Review a specific file or directory
- `/review --pr` — Review the current PR diff against base branch

## Review Categories

### 1. Security (CRITICAL)

- Hardcoded secrets, API keys, tokens, passwords
- SQL injection, command injection, XSS vectors
- Insecure deserialization
- Missing input validation at system boundaries
- Overly permissive CORS or auth settings
- Credentials in logs or error messages

### 2. Error Handling

- Bare except/catch blocks that swallow errors
- Missing error handling on external API calls
- Unchecked return values from I/O operations
- Error messages that leak internal details
- Missing retry/backoff on transient failures

### 3. Code Quality

- Functions exceeding 50 lines
- Cyclomatic complexity above 10
- Deeply nested conditionals (3+ levels)
- Dead code or unreachable branches
- Copy-paste duplication (3+ identical blocks)
- Magic numbers without named constants

### 4. Vibe Coding Antipatterns

- TODO/FIXME/HACK comments left in production code
- Console.log/print statements not behind a debug flag
- Commented-out code blocks
- Placeholder implementations
- Over-abstraction for single-use cases

### 5. Dependencies

- Known vulnerable versions
- Unused imports or dependencies
- Circular dependencies

### 6. Testing

- Changed logic without corresponding test changes
- Test files that only test happy paths
- Flaky test patterns (sleep, timing-dependent assertions)

## Output Format

For each issue found, report:

- Severity: CRITICAL / WARNING / NOTE
- Location: file:line
- Description: what the issue is
- Fix: concrete suggestion

## Verdict Rules

- Any CRITICAL issue = automatic FAIL
- 3+ warnings in the same category = FAIL
- Hardcoded secret = immediate FAIL

## MCMAP-Specific Checks

- Copilot instructions: verify plain text compliance (no emoji, markdown, curly braces)
- Solution XML: verify displaynames use displayname not description tags
- OptionSet prefixes: verify prefix_fieldname not prefixentity_prefix_fieldname
- KB documents: verify under 36K chars
- Python scripts: verify no hardcoded Dataverse URLs (must use config)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kevcofett) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
