---
name: code-prd
description: Build features when user wants to implement PRDs, write code for specs, or develop functionality. Works incrementally substory-by-substory with automatic testing and code review. Automatically loads PRD context to maintain consistency. Also writes standalone tests for any file. Use when this capability is needed.
metadata:
  author: navidemad
---

# Code PRD Implementation

Write production-quality code for PRD substories with **automatic testing, code review, and iterative refinement** until ready.

**Communication Style**: In all interactions and commit messages, be extremely concise and sacrifice grammar for the sake of concision.

## Philosophy

- **Substory-by-substory**: Implement incrementally with clear status tracking (⏳ → 🔄 → ✅)
- **Phase-level approval**: Auto-test and review each phase, get user approval before continuing
- **Auto-refinement**: Run code-review internally, fix issues, re-review until satisfied
- **Context-aware**: Load PRD context to maintain consistency (especially for expansions)
- **Flexible**: Can implement full PRD OR write standalone tests for any file

## When to Activate

This skill activates when user says things like:
- "implement [feature/PRD]"
- "build this feature"
- "code this"
- "develop [feature name]"
- "start implementation"
- "write code for [PRD/feature]"
- "write tests for [file/feature]" (standalone test mode)

## Substory Status Lifecycle

**Critical**: Substories follow this status lifecycle:

1. **⏳ Not Started** - Initial state, no work begun
2. **🔄 Implementing** - Active work in progress (set at Phase 0 or when beginning substory)
3. **✅ Completed** - Implementation, tests, and review complete

**When to update status:**
- **Phase 0**: After configuration, mark first substory as "🔄 Implementing"
- **During work**: Substory stays "🔄 Implementing" until fully done
- **After completion**: Only mark "✅ Completed" when implementation, tests, and review all pass

## UX Enhancements

This skill includes advanced UX features. **For full implementation details, read `references/ux-enhancements.md`:**

- Learning Mode (explain approach before substories)
- Smart PRD Discovery (auto-resume from last session)
- PRD Health Check (validate quality before implementing)
- Progress Visualization (visual progress bars)
- Dependency Warnings (detect blockers early)
- Smart Test Suggestions (complexity-based test plans)
- Code Review Insights (trends and gamification)
- Rollback Protection (auto-checkpoints at phases)
- Context-Aware Expansion Suggestions (data-driven next steps)
- Parallel Work Detection (merge conflict prevention)
- Adaptive Difficulty (workflow speeds up as you improve)

**See references/ux-enhancements.md for when and how to use each feature.**

## Implementation Workflow

### Phase 0: Validate Prerequisites and Configure Session

**FIRST: Check for CLAUDE.md**

```bash
if [[ ! -f "CLAUDE.md" ]]; then
    echo "❌ ERROR: CLAUDE.md file not found in project root"
    echo "This workflow requires a CLAUDE.md file. To create: type /init"
    exit 1
fi
```

**Configure session with Learning Mode:**

```
✅ CLAUDE.md found
📋 Ready to implement

💡 Session Configuration:

Learning Mode: [Currently: ${learning_mode ? "ON" : "OFF"}]
  When ON: I'll explain my approach before each substory
  When OFF: I'll implement directly without explanations

Change Learning Mode? [yes/no/default: no]:
```

**See references/ux-enhancements.md Section 1 for full Learning Mode implementation.**

**Mark first substory as "Implementing"** once configuration complete and work begins.

### Mode Detection

**1. PRD Implementation Mode** - User mentions PRD or says "implement"
   - Full workflow: phases → substories → auto-testing → review → approval

**2. Standalone Test Mode** - User says "write tests for [file/feature]"
   - Jump to Step 8, skip PRD workflow

### Step 1: Load PRD, Context, and Project Conventions

**Essential loading sequence:**

```bash
# Ensure directory structure
mkdir -p .claude/prds/context .claude/prds/archive/context .claude/checkpoints

# Gitignore checkpoints
if ! grep -q "\.claude/checkpoints" .gitignore 2>/dev/null; then
    echo ".claude/checkpoints/" >> .gitignore
fi

# Load PRD
prd_content=$(cat "$prd_file")
claude_md=$(cat "CLAUDE.md")

# Load or init context
if context_exists "$prd_file"; then
    context=$(read_context "$prd_file")
else
    init_context "$prd_file"
fi
```

**Smart PRD Discovery** - If no PRD specified:
- Check for in-progress PRD from last session (context files with recent timestamps)
- Show resume prompt with progress summary
- Offer to choose different PRD or create new

**See built-in workflow for Smart PRD Discovery details** (lines 205-278 of original).

**Determine PRD Type** (Core / Expansion / Task):

**For Core PRDs:**
- Load CLAUDE.md for project conventions
- Goal: Establish clean patterns for expansions

**For Task PRDs:**
- Work through Implementation Checklist step-by-step
- No phases/substories - checkbox completion

**For Expansion PRDs (CRITICAL - AUTO-LOAD CORE):**

1. **Validate core PRD exists and is complete**
2. **Load comprehensive core context**:
   - Read core PRD file and context JSON
   - Extract files_created, patterns, libraries, decisions
   - Read actual implementation files to analyze patterns
   - Document findings with specific code examples

3. **Present findings before coding**:
```
🔍 Core Implementation Analysis (AUTO-LOADED):

Core PRD: .claude/prds/YYYY-MM-DD-{feature}-core.md
Status: ✅ Complete

Implementation Files ([X] analyzed):
- [list files with descriptions]

Established Patterns ([Y] identified):
- [pattern name]: [description and examples]

Libraries: [Z] libraries
Architectural Decisions: [W] decisions

Code Analysis Insights:
- Naming conventions: [observed patterns]
- Error handling: [approach used]
- Validation: [approach used]

✅ Expansion will EXTEND these patterns consistently.
```

### Step 2: Analyze Existing Architecture

**Before writing code:**
- Explore existing code structure and patterns
- Identify naming conventions
- Locate similar features/components
- Document findings from CLAUDE.md and codebase

### Step 2a: PRD Health Check

**Run automated quality analysis:**

- Check acceptance criteria (20 pts)
- Check success metrics (15 pts)
- Check security considerations (15 pts)
- Check performance requirements (10 pts)
- Check error handling (10 pts)
- Check testing strategy (15 pts)
- Check substories defined (15 pts)

**Show health check with quality score:**
```
🔍 PRD Health Check

Quality Score: ${score}/100 [Excellent ✅ / Good 👍 / Acceptable ⚠️ / Needs Improvement ❌]

[If score < 60:]
⚠️  PRD quality below threshold

Options:
1. 🔧 Fix issues now
2. ⏭️  Implement anyway
3. 📋 Show detailed recommendations
```

**See built-in workflow for full health check implementation** (lines 432-543 of original).

### Step 3: Parse PRD and Create Implementation Plan

**Parse for:**
- Phases and substories
- Current status (⏳ / 🔄 / ✅)
- Dependencies
- Acceptance criteria

**Show plan with Progress Visualization:**
```
📋 Implementation Plan: [Feature Name]

Overall Progress: ░░░░░░░░░░ 0% (0/4 substories)

Phase 1: [Phase Name] (4 substories)
├─ ⏳ Substory 1.1: [Name] - Not Started
├─ ⏳ Substory 1.2: [Name] - Not Started
├─ ⏳ Substory 1.3: [Name] - Not Started
└─ ⏳ Substory 1.4: [Name] - Not Started

Ready to begin Phase 1? [yes/show-details/skip-to]
```

**For resuming in-progress:**
```
Overall Progress: ████████░░ 66% (2/3 complete)

Phase 1: [Phase Name]
├─ ✅ Substory 1.1 - Completed
├─ ✅ Substory 1.2 - Completed
└─ 🔄 Substory 1.3 - Implementing

Resume at Substory 1.3? [yes/restart-substory]
```

### Step 4: Implement Phase (Substory-by-Substory)

**For each substory:**

#### Step 4.0: Learning Mode Explanation (If Enabled)
**📚 See references/ux-enhancements.md Section 1**

If enabled, explain approach before implementing.

#### Step 4.1: Dependency Warnings Check
**⚠️ See references/ux-enhancements.md Section 2**

Check for blockers: API keys, external services, migrations, dependencies.

#### Mark Substory Implementing

**Update PRD:**
```markdown
🔄 Substory 1.1: [Name] - Implementing
  Started: YYYY-MM-DD HH:MM
  Current: [Brief status]
```

**Update context:**
```bash
set_current_phase "$prd_file" "Phase 1: Substory 1.1"
```

#### Write Code

**For Core PRDs:**
Establish clean, simple patterns.

**For Expansion PRDs (USE LOADED PATTERNS):**
- Follow established patterns exactly
- Extend (don't replace) core code
- Maintain naming/structure consistency
- Use same libraries as core

**Quality Requirements:**
- Follow CLAUDE.md conventions
- Clean, readable code
- Appropriate comments
- Consistent naming (match core for expansions)
- Proper error handling
- Input validation
- Appropriate logging

**Track changes:**
```bash
add_created_file "$prd_file" "src/models/user.model.ts"
add_decision "$prd_file" "[decision made]"
set_library "$prd_file" "[category]" "[library]"
set_pattern "$prd_file" "[pattern]" "[location/description]"
```

#### Mark Substory Complete

**Update PRD:**
```markdown
✅ Substory 1.1: [Name] - Completed (YYYY-MM-DD)
  Files: [list files]
  Summary: [brief summary]
```

#### Show Progress

```
✅ Substory 1.1 complete!

📝 Files created/modified:
- [file list with descriptions]

📊 Progress: 1/4 substories (25% of Phase 1)

⏭️  Next: Substory 1.2 - [Name]
```

**Continue to next substory** until phase complete.

#### Step 4.9: Rollback Protection Checkpoint
**💾 See references/ux-enhancements.md Section 3**

Before each new phase, create automatic checkpoint with rollback script.

### Step 5: Phase Complete - Auto-Test and Review Loop

**When all substories in phase complete:**

```
🎉 Phase 1 Complete: [Phase Name]

✅ Completed substories: [list]
📊 Phase Stats: [files, lines, patterns]

🧪 Now running testing and review...
```

#### Step 5a: Auto-Run Tests
**🧪 See references/ux-enhancements.md Section 4 for Smart Test Suggestions**

**If testing disabled in CLAUDE.md:**
```
ℹ️  Testing skipped (CLAUDE.md indicates no tests)
Proceeding to code review...
```

**If testing enabled:**

1. **Identify framework** (Jest, pytest, RSpec, Vitest, etc.)
   - Check CLAUDE.md, package.json, requirements.txt, Gemfile
   - Find test command and file patterns
   - Analyze existing test patterns

2. **Present configuration:**
```
📊 Testing Framework: [Framework]
Test command: [command]
Test pattern: [pattern]
Coverage: [requirement from CLAUDE.md]
```

3. **Write comprehensive tests:**
   - Unit tests for business logic
   - Integration tests for interactions
   - Cover all acceptance criteria
   - Test happy paths, errors, edge cases
   - Follow project test patterns

4. **Run tests:**
```bash
$test_cmd
```

5. **Report results:**
```
🧪 Tests Written and Executed:
✅ [X] tests passed
📊 Coverage: [Y]%
⏱️  Duration: [time]
```

**If tests fail** (and not skipped):
- Show failures
- Fix code
- Re-run until passing

#### Step 5b: Auto-Run Code Review (Internal)
**📋 See references/ux-enhancements.md Section 5 for Code Review Insights**

**Review Dimensions:**
1. Code Quality (readability, maintainability, complexity)
2. Architecture & Design (patterns, SOLID, consistency with core for expansions)
3. Security (auth, validation, injection prevention, secrets)
4. Performance (queries, caching, efficiency)
5. Testing Quality (coverage, edge cases, assertions)
6. Project-Specific (CLAUDE.md standards, framework conventions)

**Categorize findings:**
- 🔴 **Critical** (must fix): Security vulnerabilities, data loss, crashes
- 🟠 **Major** (should fix): Performance issues, missing validation, pattern inconsistencies
- 🟡 **Minor** (nice to have): Style issues, refactoring opportunities
- ✅ **Positive**: Highlight good practices

**Show summary:**
```
📋 Code Review Complete:

🔴 Critical: 0
🟠 Major: 2
🟡 Minor: 5
✅ Positive: 8 things done well

[List major/critical issues with file:line, risk, fix]
```

#### Step 5c: Auto-Fix Issues

**If critical/major issues found:**

```
🔧 Fixing [N] issues automatically... (Iteration 1/3)

[Fix each issue, show progress]

Re-running review...
```

**Auto-fix iterations:**
- Iteration 1: Auto-fix
- Iteration 2: Auto-fix (if issues remain)
- Iteration 3: Ask user for guidance, then fix

**If issues remain after 3 iterations:**
```
⚠️ Unable to resolve all issues after 3 iterations.

Options:
1. approve-with-issues - Continue anyway
2. manual-fix - You fix manually
3. redo-substory - Re-implement with different approach
4. get-help - Document blocker
```

### Step 6: Phase Approval Gate

**When review clean (no critical/major):**

```
🎉 Phase 1 Ready for Approval!

📊 Summary:
✅ [X] substories completed
✅ [Y] tests passing ([Z]% coverage) [or "⚠️ Tests skipped"]
✅ Code review: No critical/major issues
📝 [N] files created/modified

Changes: [summary]
Patterns established: [list]

Approve Phase 1 and continue? [yes/show-changes/redo-phase/stop]
```

**User options:**
- **yes**: Continue to next phase
- **show-changes**: Show git diff
- **redo-phase**: Re-implement
- **stop**: Stop here

#### Step 6.5: Parallel Work Detection
**🔍 See references/ux-enhancements.md Section 6**

Before next phase, check for parallel work on main branch to prevent conflicts.

### Step 7: Continuation or Completion

**If more phases:** Continue to Step 4

**If all phases complete:**

**Detect git state for contextual next steps:**
```bash
current_branch=$(git rev-parse --abbrev-ref HEAD 2>/dev/null)
base_branch=$(git symbolic-ref refs/remotes/origin/HEAD 2>/dev/null | sed 's@^refs/remotes/origin/@@')
on_main=false
has_changes=false

if [[ "$current_branch" == "main" ]] || [[ "$current_branch" == "master" ]] || [[ "$current_branch" == "$base_branch" ]]; then
    on_main=true
fi

if [[ -n $(git status --porcelain 2>/dev/null) ]]; then
    has_changes=true
fi
```

**Show completion with context-aware options:**

**For CORE PRD:**
```
🎉 Core PRD Complete!

✅ All phases implemented
📊 Core Stats: [files, tests, patterns]
🌱 Core foundation ready!

Context saved: .claude/prds/context/{prd}.json
(Auto-loaded when creating expansion PRDs)

💡 Next steps:

[Contextual options based on git state:]

[If on_main && has_changes:]
1. 🚀 Create pull request
2. 📋 Plan expansion
3. ✏️  Continue coding

[If !on_main && has_changes:]
1. 💾 Commit changes
2. 📋 Plan expansion
3. ✏️  Continue coding

[If !on_main && !has_changes:]
1. 🚀 Create pull request
2. 💾 Commit more changes
3. 📋 Plan expansion

[If on_main && !has_changes:]
**🎯 See references/ux-enhancements.md Section 7 for Context-Aware Expansion Suggestions**

1. 📋 Plan expansion:
   [Smart suggestions from code analysis]
2. ✏️  Start new feature
3. 🎯 Other

What would you like to do? [1/2/3 or describe]:
```

**For EXPANSION PRD:** Similar format with expansion-specific messaging.

### Step 8: Standalone Test Mode (No PRD)

**When user says "write tests for [file/feature]":**

1. **Identify target** (specific files or feature area)
2. **Analyze testing setup** (framework, command, patterns from CLAUDE.md and existing tests)
3. **Present test plan**
4. **Write comprehensive tests** (unit, integration, following project patterns)
5. **Run tests**
6. **Report results** with coverage

**No PRD updates, no context management - just tests.**

## Blocker Handling

If blocked:
1. Mark substory as 🔄 with blocker note
2. Document in PRD
3. Ask for resolution or skip to next non-blocked substory

## Key Principles

**This skill DOES:**
- Implement substory-by-substory with clear status tracking (⏳ → 🔄 → ✅)
- Show progress after each substory
- Auto-test and auto-review after each phase
- Auto-fix critical/major issues (up to 3 iterations)
- Ask for approval at phase boundaries
- Update PRD and context automatically
- Load core context for expansions automatically
- Write standalone tests without PRD

**This skill DOES NOT:**
- Auto-continue without approval
- Auto-commit or auto-create PRs
- Skip testing or review

**Workflow:**
```
Substories (⏳ → 🔄 → ✅) → Phase complete →
Auto-test → Auto-review → Auto-fix → Approval →
Continue or stop
```

## Directory Structure

```
.claude/
├── prds/
│   ├── YYYY-MM-DD-feature-core.md
│   ├── context/
│   │   └── YYYY-MM-DD-feature-core.json
│   └── archive/
├── user-preferences.json (GITIGNORED)
└── checkpoints/ (GITIGNORED)

.gitignore additions:
.claude/user-preferences.json
.claude/checkpoints/
```

## UX Enhancement Reference Map

At each workflow step, refer to references/ux-enhancements.md for full implementation:

| Workflow Step | Enhancement | Section |
|---------------|-------------|---------|
| Phase 0 | Learning Mode | Section 1 |
| Phase 0 | Adaptive Difficulty | Section 8 |
| Step 1 | Smart PRD Discovery | Built-in |
| Step 2a | PRD Health Check | Built-in |
| Step 3 | Progress Visualization | Built-in |
| Step 4.0 | Learning Mode Explanation | Section 1 |
| Step 4.1 | Dependency Warnings | Section 2 |
| Step 4.9 | Rollback Protection | Section 3 |
| Step 5a | Smart Test Suggestions | Section 4 |
| Step 5b | Code Review Insights | Section 5 |
| Step 6.5 | Parallel Work Detection | Section 6 |
| Step 7 | Context-Aware Expansions | Section 7 |

## Guidelines

- Work incrementally (one substory at a time)
- Track status clearly: ⏳ Not Started → 🔄 Implementing → ✅ Completed
- **Mark first substory "🔄 Implementing" after Phase 0 configuration**
- Always load PRD context and CLAUDE.md
- For expansions: auto-load and analyze core context
- Follow CLAUDE.md conventions religiously
- Update PRD status after each substory
- Update context with patterns/libraries/decisions
- Auto-test after each phase
- Auto-review and auto-fix
- Get approval at phase boundaries
- Reference references/ux-enhancements.md at callout steps

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/navidemad) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
