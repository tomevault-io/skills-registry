---
name: quality-control
description: Project-wide quality control for all codebases. Use when reviewing code quality, running audits, or before major releases. Use when this capability is needed.
metadata:
  author: legacy3
---

# Quality Control

Comprehensive project-wide quality control that orchestrates language-specific checks.

**MANDATORY:** When running this skill, you MUST also invoke `/rust-quality` and `/portal-quality` for their respective codebases. Do NOT skip the language-specific skills. See "Audit Workflow" section.

## Quick Audit Commands

```bash
# Full project audit
pnpm build && pnpm lint && pnpm test

# Rust engine audit
cd crates/engine && cargo clippy --all-targets -- -D warnings && cargo test

# Portal audit
cd apps/portal && pnpm lint && pnpm build
```

---

## Language-Specific Skills (REQUIRED)

This skill MUST delegate to specialized quality skills:

| Codebase       | Skill             | Focus                                 |
| -------------- | ----------------- | ------------------------------------- |
| `crates/`      | `/rust-quality`   | Rust patterns, clippy, error handling |
| `apps/portal/` | `/portal-quality` | TypeScript, React, Next.js patterns   |

**Do NOT skip these.** Run these skills before completing the audit.

---

## Universal Anti-Patterns

These apply to ALL code in the project:

### 1. Control Flow Without Braces

```typescript
// WRONG
if (condition) return early;
for (const x of items) process(x);

// RIGHT
if (condition) {
  return early;
}
for (const x of items) {
  process(x);
}
```

```rust
// WRONG - Rust allows this but project forbids it
if condition { return early }

// RIGHT
if condition {
    return early;
}
```

### 2. TODO/FIXME Comments

- NEVER leave TODO comments without issue reference
- If you add a TODO, create an issue and reference it: `// TODO(#123): description`
- Better: just do the work instead of leaving a TODO

### 3. Console/Print Statements

- No `console.log` in TypeScript production code
- No `println!` or `dbg!` in Rust production code
- Use proper logging (`tracing` for Rust, error boundaries for React)

### 4. Dead Code

- No commented-out code blocks
- No unused imports or variables
- No deprecated wrappers or backwards-compat shims
- No functions with `_old`, `_legacy`, `_deprecated` suffixes

### 5. Magic Numbers

```typescript
// WRONG
if (value > 85) { ... }

// RIGHT
const MAX_PERCENT = 85;
if (value > MAX_PERCENT) { ... }
```

```rust
// WRONG
let cap = 0.85;

// RIGHT
const ARMOR_CAP: f32 = 0.85;
```

### 6. Useless Comments

- Comments must add information the code doesn't already convey
- **NEVER** add doc comments that just restate the name of a constant, function, or type
- If the name is descriptive enough, no comment is needed
- Named constants are self-documenting. `const SECONDS_PER_MINUTE: f32 = 60.0;` does not need `/// Seconds per minute`

```rust
// WRONG - restates the name
/// Default critical strike damage multiplier (200% = 2x normal damage).
const DEFAULT_CRIT_MULTIPLIER: f32 = 2.0;

// RIGHT - name is self-documenting, no comment needed
const DEFAULT_CRIT_MULTIPLIER: f32 = 2.0;

// RIGHT - comment adds non-obvious context
// From WoW patch 11.1 hotfix, was 7390 before the armor rework
const ARMOR_CONSTANT: f32 = 8150.0;
```

```typescript
// WRONG - restates what the code does
/** Fetches the user data */
async function fetchUserData() { ... }

// RIGHT - no comment needed, or explain WHY not WHAT
// Two fetches needed: permissions aren't included in the user response
async function fetchUserData() { ... }
```

### 7. Type Assertions / Unsafe Casts

```typescript
// WRONG - bypasses type checking
const data = response as MyType;

// RIGHT - validate at runtime or use type guards
if (isMyType(response)) {
  const data = response;
}
```

```rust
// WRONG - unchecked cast
let index = value as usize;

// RIGHT - checked conversion
let index = usize::try_from(value)?;
```

---

## Quality Checklist

### Before Any Commit

- [ ] `pnpm build` passes (includes type checking)
- [ ] `pnpm lint` passes
- [ ] No TODO without issue reference
- [ ] No commented-out code
- [ ] No console.log/println!/dbg! statements
- [ ] Control flow has braces

### Before Any PR

- [ ] All tests pass (`pnpm test`, `cargo test`)
- [ ] No type assertions (`as Type`) in new code
- [ ] New public APIs have documentation
- [ ] Error handling is comprehensive (no `.unwrap()` in Rust)
- [ ] Performance-sensitive code has benchmarks

### Before Release

- [ ] Full audit with both language-specific skills
- [ ] Security audit (`cargo audit` for Rust)
- [ ] Bundle size check for portal
- [ ] All TODOs resolved or tracked

---

## Audit Workflow

When running a full audit:

1. **Run language-specific skills**

   Invoke these skills for comprehensive language-specific checks:
   - `/rust-quality` for `crates/` Rust code
   - `/portal-quality` for `apps/portal/` TypeScript/React code

   These skills contain detailed checks. Do not duplicate their work.

2. **Check universal anti-patterns**

   ```bash
   # TODOs without issue references
   grep -rn "TODO" --include="*.ts" --include="*.tsx" --include="*.rs" | grep -v "#[0-9]"

   # Console/print statements in production code
   grep -rn "console\." apps/portal/src/ --include="*.ts" --include="*.tsx"
   grep -rn "println!\|dbg!" crates/ --include="*.rs" | grep -v "/tests\|/cli\|test.rs"
   ```

3. **Generate unified report**

   Combine findings from both language-specific skills with universal anti-pattern results into a single report with:
   - Issue count by severity
   - Files affected
   - Specific line numbers
   - Recommended fixes

---

## Severity Levels

| Level        | Description                               | Action              |
| ------------ | ----------------------------------------- | ------------------- |
| **Critical** | Security vulnerabilities, data loss risk  | Block merge         |
| **High**     | Bugs, unsafe code, missing error handling | Fix before merge    |
| **Medium**   | Anti-patterns, missing docs, code smells  | Fix soon            |
| **Low**      | Style issues, minor improvements          | Fix when convenient |

---

## Environment File Standards

All `.env`, `.env.example`, and `.env.local` files follow these rules:

- **File header:** Single `# Service Name Configuration` comment
- **Section headers:** Use `##` (double hash) for sections
- **Section order:** Required → HTTP/Server → Database → Auth → External → Features → Scheduler → Cron → Logging (always last)
- **Required variables:** Uncommented, may have placeholder or be blank
- **Optional variables:** Commented out with default value (`# VAR=default`)
- **Variable naming:** Service prefix in SCREAMING_SNAKE_CASE (`SENTINEL_DB_MAX_CONNECTIONS`)
- **Unit suffixes:** Include units for durations (`_SECS`, `_MINUTES`, `_MS`)
- **Booleans:** Lowercase `true`/`false`
- **Secrets:** Leave blank or use `<placeholder>` in `.env.example`
- **Trailing newline:** Always end file with newline

---

## Integration

This skill works with:

- **`/rust-quality`** - Deep Rust analysis
- **`/portal-quality`** - Deep TypeScript/React analysis

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/legacy3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
