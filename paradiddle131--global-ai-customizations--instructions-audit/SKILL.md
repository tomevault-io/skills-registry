---
name: instructions-audit
description: Audit copilot-instructions.md against the actual codebase structure, conventions, and recent changes. Detects stale paths, outdated conventions, missing sections, and drift. Use when instructions feel wrong, after major refactors, or at session end for hygiene. Use when this capability is needed.
metadata:
  author: Paradiddle131
---

# Instructions Audit

Audit the project's `copilot-instructions.md` against the actual codebase to detect drift, stale references, and missing guidance.

## When This Skill is Triggered

- User invokes `audit-instructions` or asks to "review instructions"
- After major refactors that change project structure
- Periodically at session end (suggested by compound-engineer)
- When instructions seem wrong during a session

## Execution Protocol

### Step 1: Load Instructions

Read `.github/copilot-instructions.md` from the current project.

### Step 2: Cross-Reference Checks

#### 2a. Path Verification
For every file path mentioned in the instructions:
```bash
# Extract paths and check if they exist
grep -oP '`[^`]+\.(py|ts|tsx|js|json|yml|md)`' .github/copilot-instructions.md | tr -d '`' | while read p; do
  [[ -e "$p" ]] && echo "✅ $p" || echo "❌ $p (MISSING)"
done
```

#### 2b. Stack Verification
Check that listed stack versions match actual project files:
- `pyproject.toml` → Python version, dependencies
- `package.json` → Node/React/Vite versions
- `Dockerfile` → Base image versions
- `docker-compose.yml` → Service configuration

#### 2c. Structure Verification
Compare the Project Structure table against actual directory layout:
```bash
# List actual structure and compare
find backend/src frontend/src discord-bridge/src -type f -name "*.py" -o -name "*.ts" -o -name "*.tsx" | head -50
```

Identify:
- Paths in instructions that don't exist anymore
- New important files not mentioned in instructions
- Renamed or moved modules

#### 2d. Convention Verification
For each convention listed, check if the codebase actually follows it:
- "Use async/await for all I/O" → grep for sync I/O calls
- "Pydantic models for boundaries" → check if new endpoints use Pydantic
- "structlog for logging" → check for print() or stdlib logging

### Step 3: Gap Detection

Check for important aspects NOT covered by instructions:
- New directories or modules added since last update
- New dependencies that have conventions (e.g., new ORM, new framework)
- New hooks, behavioral rules, or workflow changes
- Missing test commands for new test suites

### Step 4: Report

```markdown
## Instructions Audit Report

**File**: .github/copilot-instructions.md
**Last modified**: <date>
**Staleness score**: Low/Medium/High

### ❌ Stale References (fix now)
- `backend/src/observatory/foo.py` — file no longer exists (removed in PR #XX)
- Python 3.11 listed but pyproject.toml requires 3.12+

### ⚠️ Missing Coverage (add)
- `backend/src/observatory/new_module.py` — not mentioned in Project Structure
- Discord Bridge now has `vision.py` — not documented

### ✅ Verified Correct
- Stack versions match
- Git workflow rules still apply
- Convention compliance: 8/8 checked

### 💡 Suggestions
- Add section for new `analytics_engine.py` module
- Update Docker port if changed
```

### Step 5: Auto-Fix

For stale references and missing coverage:
1. Update file paths that have moved
2. Add new modules to Project Structure table
3. Update version numbers
4. Add any new conventions discovered during audit

Present changes to user before committing.

## Notes

- This skill inspects the instructions file ONLY — it does not modify the codebase
- The audit is not exhaustive; it focuses on high-impact drift
- Run at least once per major PR or refactor

---
> Source: [Paradiddle131/global-ai-customizations](https://github.com/Paradiddle131/global-ai-customizations) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
