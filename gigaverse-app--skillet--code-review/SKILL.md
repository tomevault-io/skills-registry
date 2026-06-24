---
name: code-review
description: Use when asked to "review code", "review PR", "review diff", "check this PR", "do a code review", or mentions "code review", "PR review", "full diff", "cross-file patterns", "code quality", "review changes".
metadata:
  author: gigaverse-app
---

# Code Review Best Practices

## Core Philosophy

**Review at PR level, not file-by-file. Focus on egregious structural issues, not minutia. Look for cross-file patterns that indicate architectural problems.**

## Quick Start

```bash
# ALWAYS filter out lock files first (often 10k+ lines of noise)
git diff main...HEAD -- . ':!uv.lock' ':!poetry.lock' > /tmp/pr-diff.txt
wc -lc /tmp/pr-diff.txt  # Most diffs fit in 256KB after this

# Check size - if under 256KB, analyze the whole thing
# If still too big, filter more
git diff main...HEAD -- . ':!uv.lock' ':!docs/*' > /tmp/code-diff.txt
```

## What to Look For - Egregious Issues Only

### Quick Checklist
- [ ] **Code duplication** - Same logic in 2+ places?
- [ ] **Repeated patterns** - Same code structure 3+ times?
- [ ] **God functions** - Functions over 100 lines?
- [ ] **Weak types** - Any, object, raw dict/list, missing annotations?
- [ ] **Type-deficient patterns** - hasattr, getattr instead of proper types?
- [ ] **Meaningless tests** - Tests that just verify assignment?
- [ ] **Dead code** - Unused functions, commented code?

### What NOT to Flag
- Variable naming (unless truly confusing)
- Line length
- Comment style
- Whitespace
- Import order

These are auto-fixable or minor. Focus on structural problems.

## Critical Rules Quick Reference

```python
# Flag: Weak types
def process(data: Any) -> dict:  # Any, raw dict
def process(data: UserData) -> UserResponse:  # Specific types

# Flag: hasattr/getattr (type deficiency marker)
if hasattr(obj, "name"):  # Why don't you know the type?
if isinstance(obj, Named):  # Use protocol or union type

# Flag: God functions
def process_order(order_data):  # 200 lines doing everything
    validated = _validate(data)  # Extract responsibilities
    transformed = _transform(validated)

# Flag: Duplicate logic across files
# service_a.py and service_b.py have same 20-line validation
# Extract to shared utility

# Flag: Related classes without shared interface
class CacheDirectory:
    def list_entries(self): ...  # Names differ
class StatusDirectory:
    def get_all_entries(self): ...  # Should share ABC
```

## Handling Large Diffs

**Keep maximum context to spot cross-file patterns.**

### Step 1: Remove Lock Files (Always)
```bash
git diff main...HEAD -- . ':!uv.lock' > /tmp/diff.txt
wc -lc /tmp/diff.txt  # Under 256KB? STOP - analyze whole thing
```

### Step 2: Remove Docs (If Still Too Big)
```bash
git diff main...HEAD -- . ':!uv.lock' ':!docs/*' > /tmp/code-diff.txt
```

### Step 3: Remove Tests (Last Resort)
```bash
git diff main...HEAD -- . ':!uv.lock' ':!docs/*' ':!tests/*' > /tmp/prod.txt
```

### Step 4: Chunk with LARGE Chunks (Rare)
```bash
# Only if >256KB after all filtering
# Use 15k lines to preserve cross-file patterns
# Small chunks (2k lines) defeat the purpose!
```

## LLM-Based Full Diff Review

**Why use external LLM tools?** Claude Code has a 25K context limit per tool call. For reviewing entire PR diffs (50K-200K+ tokens), use models with larger context windows.

**Benefits:**
1. Cross-file pattern detection in one pass
2. Different models catch different issues
3. Faster than file-by-file review

### Workflow

```bash
# 1. Extract changes (handles lock file exclusion automatically)
./scripts/extract-changes.sh              # PR: current branch vs main
./scripts/extract-changes.sh abc123       # Specific commit
./scripts/extract-changes.sh auth/login   # Path pattern (fuzzy match)

# 2. Run code review with large-context model
./scripts/llm-review.sh -m gpt-4o
./scripts/llm-review.sh -m claude-3-5-sonnet-latest

# 3. Run specialized reviews
./scripts/llm-review-tests.sh -m gpt-4o      # Test quality
./scripts/llm-review-types.sh -m gpt-4o      # Type hints
```

### Setup

Requires [Simon Willison's llm tool](https://llm.datasette.io/):

```bash
pip install llm
llm keys set openai          # For GPT-4
pip install llm-claude-3     # For Claude
llm keys set claude
```

See [references/llm-tooling.md](references/llm-tooling.md) for full setup and usage guide.

### Using LLM Findings

LLM review provides **hints**, not final answers:
1. Identify areas to investigate deeper
2. Cross-check with different models
3. Use Claude Code to implement actual fixes

## Reference Files

For detailed patterns and examples:
- [references/what-to-flag.md](references/what-to-flag.md) - Duplication, weak types, god functions
- [references/code-bloat-patterns.md](references/code-bloat-patterns.md) - AI-generated bloat patterns
- [references/llm-tooling.md](references/llm-tooling.md) - LLM tool setup and usage for full-diff review

**Remember**: The ENTIRE POINT of full diff review is cross-file patterns. Don't chunk unless you absolutely must!

## Human-in-the-Loop After Code Review

**CRITICAL:** After completing a code review, ALWAYS:

1. **Present findings** - Output the full review report with all sections
2. **List suggested actions** - Number each potential fix
3. **Ask for approval** - Use AskUserQuestion before creating TODOs or executing
4. **Wait for explicit approval** - User may reject, ask for clarification, or approve selectively

**Why this matters:**
- LLM reviews often suggest unnecessary changes
- Some suggestions may be wrong or over-engineered
- User knows context that LLM doesn't
- Executing without approval wastes time on rejected work

**Example flow:**
```
[Code review findings output]

## Suggested Actions
1. Add format-duration validator to Schema
2. Add tests for format-duration validation
3. Move constant to config class

Which items should I proceed with?
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gigaverse-app) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
