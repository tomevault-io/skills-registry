---
name: forge
description: Lean code implementation pair. Claude (Smith) writes code, Gemini (Temper) reviews for bugs, security, and edge cases. ~2x token cost for high-quality code output. Use when this capability is needed.
metadata:
  author: stephenwinters81
---

# The Forge: Code Implementation Pair

## When to Activate
Use this skill when the user requests:
- Code implementation tasks
- Bug fixes requiring quality review
- Feature additions with security considerations
- Any coding task where review overhead is acceptable
- User explicitly mentions "forge", "pair programming", or "implement with review"

## Architecture
**Pair: Smith + Temper**

### The Smith (Claude)
- **Role:** Implementer
- Writes code based on user requirements
- Follows existing patterns in codebase
- Produces complete, working implementation

### The Temper (Gemini 3 Pro)
- **Role:** Adversarial Reviewer
- Reviews Smith's code for bugs
- Checks for security vulnerabilities
- Identifies edge cases and missing error handling
- Returns PASS or FAIL with specific issues

## Execution Flow
```
[User Request]
    ↓
[Smith] → Writes implementation
    ↓
[Temper] → Reviews code
    ↓
    ├── PASS → Return code to user
    └── FAIL → Smith fixes issues → Temper re-reviews
```

## Invocation

```bash
python3 ~/.claude/skills/forge/forge.py "Your implementation task"
```

## Options

```bash
python3 ~/.claude/skills/forge/forge.py "Your task" --max-iterations 3 --verbose
```

## Example Usage

User: "Add input validation to the login form"

```bash
python3 ~/.claude/skills/forge/forge.py "Add input validation to the login form in web/components/auth/LoginForm.tsx - validate email format and password length"
```

## Why This Works
1. **Cross-Model Review**: Gemini catches Claude's blind spots
2. **Lean Overhead**: Only 2 agents vs 3+ in other formations
3. **Focused Scope**: No planning phase - just implement and review
4. **Iterative Fix**: Failed reviews trigger targeted fixes

## Token Efficiency
- ~2x base cost (1 implementation + 1 review)
- Best for: Medium complexity, single-file or few-file changes
- Avoid for: Large architectural changes (use Foundry instead)

## Requirements
- Claude Code CLI installed and authenticated (`claude` command available)
- Gemini CLI installed (`gemini` command available)
- Python package: `rich` (for formatted output)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stephenwinters81) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
