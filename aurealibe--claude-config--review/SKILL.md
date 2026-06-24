---
name: review
description: Universal code review - auto-detects scope from git changes, spec, commit, or brief Use when this capability is needed.
metadata:
  author: aurealibe
---

**YOU ARE EXECUTING THE `/review` SKILL.** The user triggered this skill. Follow ALL instructions below step by step. Do NOT treat this as a freeform conversation - execute the skill workflow.

Think carefully for code review. Follow CLAUDE.md rules.

## Critical Instruction

**YOU MUST use the Task tool with subagent_type to launch agents. NEVER read or analyze code files directly yourself.**
Agents do the heavy lifting - you orchestrate and aggregate their results.

## Tool Strategy

Use Task tool with these subagent_type values:
- `explore-docs` - Documentation lookup
- `explore-codebase` - Code pattern search
- `explore-db` - Database schema exploration
- `backend-code-optimizer` - Go code quality analysis (returns score + issues)
- `frontend-code-reviewer` - React/TS code quality analysis (returns score + issues)

---

## 1. DETERMINE SOURCE & SCOPE

### Spec Handling

- **Spec alone** (`/review spec:file.md`): Extract file list FROM the spec (exclusive source)
- **Spec + other source** (`/review spec:file.md mode:local`): Spec provides CONTEXT, other source determines files

### File Source Priority (use first available):

1. `$ARGUMENTS.feature` -> Search codebase for feature-related files
2. `$ARGUMENTS.commit` -> `git show --name-only $ARGUMENTS.commit`
3. `$ARGUMENTS.mode` -> Git diff (pushed or local)
4. `$ARGUMENTS.spec` alone -> Extract files from spec
5. **Default**: `mode=local` (uncommitted changes)

### If mode = "pushed" (or default when no args and clean working tree)

```bash
git log origin/$(git rev-parse --abbrev-ref HEAD) -1 --format="%H %s"
git show --name-only --format="" HEAD
git show HEAD
```

### If mode = "local" (default)

```bash
git diff origin/$(git rev-parse --abbrev-ref HEAD)...HEAD --name-only
git status --porcelain
git diff HEAD
```

### If spec provided

- Read the spec file for context (requirements, architecture, expected behavior)
- **Spec alone**: Extract all referenced files from spec as review targets
- **Spec + other source**: Use spec as context only, files come from other source

### If commit provided

```bash
git show --name-only $ARGUMENTS.commit
git show $ARGUMENTS.commit
```

### If feature provided

**This is a feature-based review, independent of git changes.**

1. Use `Task(subagent_type="explore-codebase")` to find all files related to the feature
2. Apply scope filter if provided (backend -> `backend/**/*.go`, frontend -> `frontend/src/**/*.{ts,tsx}`)
3. Collect the file list from agent response -> these become the review targets

### Auto-Categorize Files

| Pattern | Domain |
|---------|--------|
| `backend/**/*.go` | Backend |
| `frontend/src/**/*.{ts,tsx}` | Frontend |
| Other | Config/Docs |

**Apply forced scope if provided:** `$ARGUMENTS.scope`

### Report Scope

```markdown
## Review Scope

**Source**: [feature/mode/spec/commit]
**Feature**: [feature name if provided, otherwise "N/A"]
**Spec Context**: [spec file path if provided, otherwise "None"]
**Commit**: [hash if applicable]
**Detected Scope**: [backend/frontend/both]

### Backend ([count] files)
- [file list]

### Frontend ([count] files)
- [file list]

### Other ([count] files)
- [file list]
```

---

## 2. EXPLORE (PARALLEL)

**MANDATORY: Use Task tool with subagent_type for each agent. Do NOT read files directly.**

Launch agents in a **single message** (parallel execution). Scale agent count to review scope.

### Backend Exploration (when backend files present, min 2 agents)

1. **Code review** - `Task(subagent_type="explore-codebase", prompt="Read and review these Go files for correctness: [file list]. Check error handling, nil safety, context propagation, business logic")`
2. **Pattern comparison** - `Task(subagent_type="explore-codebase", prompt="Find similar existing patterns in backend/ that match [changed files]. Compare conventions, naming, error handling")`
3. **Architecture check** (multi-file changes) - `Task(subagent_type="explore-codebase", prompt="Check Clean Architecture compliance: verify [changed files] respect layer boundaries. Check imports, dependency direction")`
4. **Duplication scan** (new code) - `Task(subagent_type="explore-codebase", prompt="Search backend/ for existing code that duplicates functionality in [new files]. Check reusable helpers and patterns")`

### Frontend Exploration (when frontend files present, min 2 agents)

1. **Code review** - `Task(subagent_type="explore-codebase", prompt="Read and review these React/TS files for correctness: [file list]. Check hook deps, TypeScript types, error handling, i18n")`
2. **Pattern comparison** - `Task(subagent_type="explore-codebase", prompt="Find similar existing components/hooks in frontend/src/ that match [changed files]. Compare patterns, props, state management")`
3. **Component quality** (UI changes) - `Task(subagent_type="explore-codebase", prompt="Check [changed components]: Shadcn UI usage, accessibility, responsive design, loading/error states")`
4. **Duplication scan** (new code) - `Task(subagent_type="explore-codebase", prompt="Search frontend/src/ for existing code that duplicates functionality in [new files]")`

### Supporting Exploration (as needed)

| Condition | Task tool call |
|-----------|----------------|
| Database changes | `Task(subagent_type="explore-db", prompt="dev - Check tables related to [feature]. Verify schema, constraints, RLS")` |
| External library | `Task(subagent_type="explore-docs", prompt="[library] [specific API] best practices")` |

---

## 2.5 POST-EXPLORATION CHECK

After agents return, verify coverage before deep analysis:

1. **Full code path understood?** Can I trace every changed function from entry to data layer? If gaps -> launch targeted `explore-codebase`
2. **Reference patterns found?** At least one existing impl to compare against? If not -> launch `explore-codebase`
3. **Database context complete?** If changes touch data ops, do I know the schema? If not -> launch `explore-db`
4. **Library APIs verified?** If not -> launch `explore-docs`

**-> NOW PROCEED TO SECTION 2.6 (MANDATORY)**

---

## 2.6 DEEP ANALYSIS (PARALLEL) - MANDATORY STEP

**CRITICAL: You MUST execute this step after exploration. This is where the actual code review happens.**

**Use Task tool with subagent_type. Do NOT read or analyze files yourself - the agents do this.**

Launch specialized agents based on detected scope. Skip only if no files exist for that domain.

### Scope Detection Logic

1. If `$ARGUMENTS.scope` is set -> use only that scope
2. Else auto-detect from file patterns

### Agent Dispatch (single message, parallel if both scopes)

**Include spec context and aggregated exploration results in prompt.**

| Condition | Task tool call |
|-----------|----------------|
| Backend scope active AND backend files present | `Task(subagent_type="backend-code-optimizer", prompt="Analyze these Go files for quality: [file list]. Context from exploration: [aggregate ALL results from backend exploration agents - patterns found, architecture issues spotted, duplications identified, reference implementations]. Spec context: [spec summary if available]. Provide quality score and categorized issues.")` |
| Frontend scope active AND frontend files present | `Task(subagent_type="frontend-code-reviewer", prompt="Review these React/TypeScript files: [file list]. Context from exploration: [aggregate ALL results from frontend exploration agents - patterns found, component quality issues, duplications identified, reference implementations]. Spec context: [spec summary if available]. Provide quality score and categorized issues.")` |

### Skip Conditions

- If `scope=frontend` -> skip `backend-code-optimizer` entirely
- If `scope=backend` -> skip `frontend-code-reviewer` entirely
- If no frontend files changed -> skip `frontend-code-reviewer`
- If no backend files changed -> skip `backend-code-optimizer`
- If no files changed at all -> report "No files to review" and stop

**Agent outputs to integrate:**
- Quality scores -> merge into section 4
- Issues categorized as Critical/Important/Nice-to-have -> merge into section 5
- Optimization recommendations

---

## 3. AGGREGATE RESULTS

**Aggregate and format the results from section 2.6 agents.** Do NOT re-analyze files yourself.

Use the analysis format from [checklists/backend-checklist.md](checklists/backend-checklist.md) and [checklists/frontend-checklist.md](checklists/frontend-checklist.md).

```markdown
## Code Review Analysis

### Files Reviewed (from agent reports)
- `path/to/file` - [purpose of changes]
```

---

## 4. QUALITY SCORES

Aggregate scores from specialized agents (section 2.6). Skip domains with no agent output.

Use scoring criteria from checklists.

### Global Score

Weighted average based on file count per domain.

```markdown
## Global Quality Score: X/100

Formula: (Backend Score x backend_files + Frontend Score x frontend_files) / total_files

[1-2 sentence overall assessment]
```

---

## 5. PROPOSE FIXES

```markdown
## Recommended Fixes

### Critical (must fix)
1. **[Issue]**
   - File: `path:line`
   - Problem: [description]
   - Fix: [how to fix]

### Important (should fix)
1. ...

### Nice-to-have
1. ...
```

---

## 6. VALIDATE

Ask with AskUserQuestion: "Code review complete. What would you like to do?"

Options:
- "Apply all fixes"
- "Apply Critical only"
- "Apply Critical + Important"
- "No fixes needed"

---

## 7. IMPLEMENT (if requested)

**This is the core purpose of the review command: identify issues AND fix them.**

After user validation, implement fixes directly using Edit tool. Do NOT just report - actually modify the files.

**Order:**
1. Critical issues first
2. Important issues
3. Nice-to-have (if approved)

**Implementation approach:**
- Use Edit tool to apply each fix
- Include simplification recommendations from agents
- Apply one fix at a time, verify it doesn't break anything
- Track progress with TodoWrite

**Rules:**
- Do not break existing functionality
- Do not change business logic unless it's a bug
- Keep changes minimal but complete
- Apply ALL approved fixes, don't stop halfway
- If logic seems wrong, ASK before changing

---

## 8. VERIFY

Run verification based on scope:

```bash
# Backend (if backend files changed)
cd backend && go build ./... && go vet ./...

# Frontend (if frontend files changed)
cd frontend && npm run build
```

Database (if schema changes): `mcp__supabase-dev__get_advisors`

---

## 9. SUMMARY

Use the summary format from [templates/review-report.md](templates/review-report.md).

---

## Rules

- **USE AGENTS** - Task tool with subagent_type, never analyze directly
- **EXPLORE FIRST** - parallel agents before analysis
- **MEASURE** - provide quality scores per domain
- **PRIORITIZE** - Critical > Important > Nice-to-have
- **SIMPLIFY** - include simplification recommendations from agents
- **FIX WHAT YOU FIND** - after user approval, actually implement fixes with Edit tool
- **MINIMAL CHANGES** - fix issues, don't refactor unless asked
- **CHECK i18n** - verify next-intl for all frontend text
- **VERIFY BUILDS** - run build commands before completing

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aurealibe) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
