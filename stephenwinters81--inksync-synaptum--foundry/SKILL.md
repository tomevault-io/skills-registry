---
name: foundry
description: Three-stage code implementation with planning. Gemini (Engineer) plans, Claude (Caster) implements, Gemini (Inspector) verifies. ~3x token cost for complex, multi-file changes. Use when this capability is needed.
metadata:
  author: stephenwinters81
---

# The Foundry: Code Implementation Triple

## When to Activate
Use this skill when the user requests:
- Complex multi-file implementations
- Architectural changes requiring planning
- Features with significant design decisions
- Tasks where upfront planning reduces rework
- User explicitly mentions "foundry", "plan and implement", or "complex implementation"

## Architecture
**Triple: Engineer + Caster + Inspector**

### The Engineer (Gemini 3 Pro)
- **Role:** Planner/Architect
- Analyzes requirements and codebase context
- Creates implementation blueprint with steps
- Identifies files to modify and dependencies
- Sets success criteria for verification

### The Caster (Claude)
- **Role:** Implementer
- Executes the Engineer's blueprint
- Writes production-ready code
- Follows existing patterns in codebase

### The Inspector (Gemini 3 Pro)
- **Role:** Verifier
- Compares implementation against blueprint
- Verifies all requirements met
- Checks for bugs, security, edge cases
- Returns PASS or identifies gaps

## Execution Flow
```
[User Request]
    ↓
[Engineer] → Creates blueprint
    ↓
[Caster] → Implements code
    ↓
[Inspector] → Verifies against blueprint
    ↓
    ├── PASS → Return code to user
    └── FAIL → Caster fixes → Inspector re-verifies
```

## Invocation

```bash
python3 ~/.claude/skills/foundry/foundry.py "Your complex implementation task"
```

## Options

```bash
python3 ~/.claude/skills/foundry/foundry.py "Your task" --max-iterations 3 --verbose --skip-planning
```

## Example Usage

User: "Add user authentication with JWT tokens"

```bash
python3 ~/.claude/skills/foundry/foundry.py "Add JWT authentication to the API - create login endpoint, middleware for protected routes, and token refresh logic"
```

## Why This Works
1. **Upfront Planning**: Engineer catches design issues before coding
2. **Cross-Model Verification**: Gemini plans and verifies, Claude implements
3. **Blueprint Alignment**: Inspector checks against original plan, not just code quality
4. **Fewer Iterations**: Good planning reduces fix cycles

## Token Efficiency
- ~3x base cost (1 plan + 1 implementation + 1 verification)
- Best for: Multi-file changes, new features, architectural work
- Avoid for: Simple bug fixes (use Forge instead)

## When to Use Forge vs Foundry

| Scenario | Recommended |
|----------|-------------|
| Single-file bug fix | Forge |
| Add validation to form | Forge |
| New API endpoint | Foundry |
| Refactor auth system | Foundry |
| Multi-component feature | Foundry |

## Requirements
- Claude Code CLI installed and authenticated (`claude` command available)
- Gemini CLI installed (`gemini` command available)
- Python package: `rich` (for formatted output)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stephenwinters81) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
