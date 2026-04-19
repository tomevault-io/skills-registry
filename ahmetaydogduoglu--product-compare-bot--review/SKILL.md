---
name: code-review
description: > Use when this capability is needed.
metadata:
  author: ahmetaydogduoglu
---

# Code Review Skill

This skill systematically reviews a codebase and produces a two-phase output:
1. **Report** — A markdown report with findings, severity levels, and explanations
2. **Fix** — Apply suggested fixes after user approval

## Supported Projects

| Project Type | Tech Stack | Key Focus Areas |
|---|---|---|
| API | Node.js, Express, JavaScript | Route security, middleware, error handling, async/await, DB queries |
| Frontend | Svelte, WebComponents | Reactivity, lifecycle, DOM manipulation, prop management, accessibility |

## When Review is Triggered

1. Read `CLAUDE.md` first — learn the project rules and structure
2. Scan the file/folder specified by the user, or the entire project
3. Follow the review pipeline below

---

## Review Pipeline

### Step 1: Determine Scope

Focus on what the user specified. If nothing was specified:

- Check recent changes with `git diff` or `git diff --staged`
- If no changes exist or the user said "review the whole project", scan all source files
- Skip these files: `node_modules/`, `dist/`, `build/`, `.env`, `package-lock.json`, `*.min.js`

```bash
# Find changed files
git diff --name-only HEAD~1
# Or all source files
find src/ -type f \( -name "*.js" -o -name "*.svelte" -o -name "*.ts" \)
```

### Step 2: Read and Analyze Files

Read each file and examine it across the 4 categories below. Assign a severity level to each finding:

| Level | Emoji | Meaning |
|---|---|---|
| Critical | 🔴 | Must fix immediately — bug, security vulnerability, data loss risk |
| Warning | 🟡 | Should fix — performance issue, bad practice, potential bug |
| Suggestion | 🔵 | Improvement opportunity — readability, refactoring, best practice |
| Info | ⚪ | Note — alternative approach, explanation, or praise |

### Step 3: Category-Based Checklists

#### 🐛 Bugs & Errors

**General:**
- Uncaught exceptions, missing try/catch blocks
- Variables not checked for null/undefined
- Incorrect logical conditions (off-by-one errors, wrong operators)
- Race condition risks
- Incorrect or missing error handling

**Express API specific:**
- Uncaught Promise rejections in `async` route handlers
- Middleware that never calls `next()`
- Sending a response after one has already been sent (code after `res.send()`)
- Missing `return` statements in route handlers

**Svelte specific:**
- Reactivity issues (updates not triggered by `$:`)
- `onMount` / `onDestroy` lifecycle errors
- Event listeners not cleaned up on component destruction
- Mutation issues on bound variables

#### ⚡ Performance

**General:**
- Unnecessary loops, nested loops (O(n²) and above)
- Memory leak risks (uncleared listeners, timers, subscriptions)
- Missing pagination/limits on large data sets

**Express API specific:**
- N+1 query problems
- Middleware ordering (heavy middleware running unnecessarily early)
- Returning unnecessarily large payloads in responses
- Missing caching opportunities
- Synchronous file operations (`fs.readFileSync` etc.)

**Svelte specific:**
- Reactive statements triggering unnecessary re-renders
- Missing `key` in `each` blocks
- Missing virtualization for large lists
- Heavy computations inside components (should use derived stores or memoization)

#### 📖 Clean Code & Readability

- Function length (evaluate if 30+ lines should be broken up)
- Unclear variable/function names
- Magic numbers and hardcoded strings
- Repeated code blocks (DRY violations)
- Missing or misleading comments
- Inconsistent code style (naming conventions, indentation)
- File organization and modularity
- Complex conditionals (could be simplified with early returns)

#### 🔒 Security

**Express API specific (priority):**
- SQL/NoSQL injection risks (unparameterized queries)
- XSS vulnerabilities (unsanitized user input)
- CORS configuration (overly permissive `*` usage)
- Missing rate limiting
- Authentication/authorization bypass risks
- Logging sensitive data (passwords, tokens, etc.)
- Missing security middleware (Helmet, CSRF protection)
- Hardcoded secrets in `.env` or source files
- Vulnerable dependencies

**Svelte specific:**
- Rendering unsanitized content with `{@html}`
- Storing sensitive data on the client side
- Injection risks in event handlers

---

## Step 4: Present Findings

Do NOT create a separate report file. Present findings directly in the conversation.

### Format

Start with a quick summary table:

| Category | 🔴 Critical | 🟡 Warning | 🔵 Suggestion | ⚪ Info |
|---|---|---|---|---|
| Bugs & Errors | X | X | X | X |
| Performance | X | X | X | X |
| Readability | X | X | X | X |
| Security | X | X | X | X |

Then list findings grouped by severity (🔴 first, then 🟡, 🔵, ⚪). For each finding:

- **File:** `path/to/file.js:line`
- **Issue:** What's wrong and why it matters
- **Fix:** What the fix would look like

Keep it concise — no need for lengthy explanations. After presenting all findings, immediately ask:

> "Should I go ahead and fix these? All of them, or specific ones?"

### Presentation Rules

- Every finding must be concrete — include file name and line number
- Don't just state the problem, explain why it's a problem
- Include a fix suggestion for every finding
- If the same pattern repeats across multiple files, group them into a single finding
- Note positive things too — highlight well-written sections as ⚪ Info

---

## Step 5: Apply Fixes

After user approval:

1. Fix **Critical** findings first
2. Fix **Warning** findings next
3. Fix **Suggestion** findings only if the user requests it
4. Briefly explain what changed after each fix

---

## Special Cases

### Only specific files requested for review
Focus on those files only, but consider their dependencies for context.

### "Quick review" requested
Focus only on 🔴 Critical and 🟡 Warning levels. Keep the report short.

### PR / commit review requested
Focus on `git diff` output. Only review changed lines, but read surrounding code for context.

### Security-focused review requested
Go deeper on the security category. Apply the OWASP Top 10 checklist. Run a dependency audit:
```bash
npm audit
```

---

## What NOT To Do

- Don't flag stylistic preferences as critical findings
- Don't push unnecessary refactoring on working code
- Don't make suggestions that contradict the project's existing conventions (check CLAUDE.md)
- Don't review test files with the same strictness as production code
- Never apply fixes without asking the user first

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ahmetaydogduoglu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
