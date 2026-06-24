---
name: project-workflow-build-system
description: Guides AI agents through TDD workflow, build system safety, git operations, and TODO management for gem development. Use this when starting development sessions, managing tasks, or needing workflow clarification. Use when this capability is needed.
metadata:
  author: bash0c7
---

# Project Workflow & Build System

Development workflow, build system permissions, and git safety protocols for PicoRuby development.

## Your Role

**You are the developer of the `pra` gem** — a CLI tool for PicoRuby application development on ESP32.

- **Primary role**: Implement and maintain the `pra` gem itself
- **User perspective**: Temporarily adopt when designing user-facing features (commands, templates, documentation)
- **Key distinction**:
  - Files in `lib/picotorokko/`, `test/`, gem configuration → You develop these
  - Files in `docs/github-actions/`, templates → These are for `pra` users (not executed during gem development)
  - When `pra` commands are incomplete, add to TODO.md — don't rush implementation unless explicitly required

## Directory Structure

```
.
├── lib/picotorokko/                   # Gem implementation
├── test/                      # Test suite
├── docs/github-actions/       # User-facing templates
├── storage/home/              # Example application code
├── patch/                     # Repository patches
├── .cache/                    # Cached repositories (git-ignored)
├── build/                     # Build environments (git-ignored)
└── TODO.md                    # Task tracking
```

## Rake Commands Permissions

### ✅ Always Allowed (Safe, Read-Only)

```bash
rake monitor      # Watch UART output in real-time
rake check_env    # Verify ESP32 and build environment
```

### ❓ Ask First (Time-Consuming)

```bash
rake build        # Compile firmware (2-5 min)
rake cleanbuild   # Clean + rebuild
rake flash        # Upload to hardware (requires device)
```

### 🚫 Never Execute (Destructive)

```bash
rake init         # Contains git reset --hard
rake update       # Destructive git operations
rake buildall     # Combines destructive ops
```

**Rationale**: Protect work-in-progress from accidental `git reset --hard`.

## Git Safety Protocol

- ✅ Use `commit` subagent for all commits
- ❌ Never: `git push`, `git push --force`, raw `git commit`
- ❌ Never: `git reset --hard`, `git rebase -i`
- ✅ Safe: `git status`, `git log`, `git diff`

## Session Flow: Tidy First + TDD + RuboCop + TODO Management

### Understanding TODO.md Structure (CRITICAL)

**Important**: TODO.md is NOT just a checklist — it's a **workflow guide** supporting t-wada style TDD.

**Key concepts**:
- **Each TODO task** = exactly one Micro-Cycle (1-5 minutes)
- **Phase structure** = Organized chunks of related tasks
- **[TODO-INFRASTRUCTURE-*] markers** = Cross-phase dependencies that MUST be resolved before proceeding
- **Test-first architecture** = Phase 0 (Test Infrastructure) comes BEFORE all feature work

**When starting a phase**:
1. Read the phase description carefully
2. Look for "⚠️ Check for [TODO-INFRASTRUCTURE-*]" warnings
3. If any [TODO-INFRASTRUCTURE-*] markers exist from previous phases:
   - STOP
   - Review what they mean
   - Resolve them in TDD cycles BEFORE proceeding
4. Start first task in the phase

**Example from picotorokko refactoring**:
- Phase 0 discovers: `[TODO-INFRASTRUCTURE-DEVICE-COMMAND]` (Thor env name parsing)
- Phase 2 proceeds normally
- Phase 5 has warning: "Check [TODO-INFRASTRUCTURE-DEVICE-COMMAND] before starting"
- Must resolve in Phase 5 TDD before moving to documentation

### Micro-Cycle (1-5 minutes per iteration)

**Goal**: Complete one Red-Green-RuboCop-Refactor-Commit cycle per TODO task

```
1. RED: Write one failing test
   bundle exec rake test → Verify failure ❌

2. GREEN: Write minimal code to pass test
   bundle exec rake test → Verify pass ✅
   bundle exec rubocop -A → Auto-fix violations

3. REFACTOR: Improve code quality
   - Apply Tidy First principles (guard clauses, symmetry, clarity)
   - Fix remaining RuboCop violations manually
   - Understand WHY each violation exists
   - bundle exec rubocop → Verify 0 violations

4. VERIFY & COMMIT: All quality gates must pass
   bundle exec rake ci → Tests + RuboCop + Coverage ✅
   Use `commit` subagent with clear, imperative message

5. UPDATE TODO.md
   - Immediately mark task complete
   - Record any [TODO-INFRASTRUCTURE-*] discoveries
   - Move to next task
```

### Quality Gates (ALL must pass before commit)

```bash
# Gate 1: Tests pass
bundle exec rake test
✅ Expected: All tests pass

# Gate 2: RuboCop: 0 violations
bundle exec rubocop
✅ Expected: "26 files inspected, 0 offenses"

# Gate 3: Coverage (CI mode)
bundle exec rake ci
✅ Expected: Line: ≥ 80%, Branch: ≥ 50%
```

### Macro-Cycle (Phase completion)

```
1. Read TODO.md phase description
   - Check for [TODO-INFRASTRUCTURE-*] warnings
   - Understand task granularity (each = 1-5 min)

2. Check for unresolved [TODO-INFRASTRUCTURE-*] markers
   - If found: Resolve in TDD cycles BEFORE proceeding
   - If none: Proceed to first task

3. Repeat Micro-Cycle for each task in phase
   - Each task is exactly one TDD cycle
   - Commit after each cycle
   - Update TODO.md immediately (remove completed tasks)
   - Record any infrastructure issues with [TODO-INFRASTRUCTURE-*]

4. After phase completion
   - Verify all [TODO-INFRASTRUCTURE-*] markers from this phase resolved
   - Verify no hanging infrastructure markers
   - If new [TODO-INFRASTRUCTURE-*] created: Mark which phase will handle it

5. User verifies
   - Full test suite passes: `rake ci`
   - Manual testing if needed
   - Code review if applicable
```

### Key Principles

**Test-First Architecture (Phase 0 Priority)**
- Phase 0: Test Infrastructure (HIGHEST PRIORITY, 3-4 days)
- Establishes solid test foundation BEFORE any feature work
- All [TODO-INFRASTRUCTURE-*] issues resolved early
- Unblocks downstream phases for clean TDD

**[TODO-INFRASTRUCTURE-*] Marker Discipline**
- 🚨 NEVER skip markers — STOP and resolve immediately
- 📌 Found in phase descriptions with specific context
- 📝 Record new ones during implementation
- ✅ Must be resolved before final verification

**Tidy First (Kent Beck)**
- Small refactoring steps (1-5 minutes each)
- Each step improves code understanding
- Changes compound into massive improvements without risk
- Example: Extract constant, rename variable, simplify guard clause

**t-wada style TDD**
- One test at a time
- Minimal code to pass (no gold-plating)
- Red-Green-Refactor cycle is fast
- Test is always green after Refactor phase
- Each TODO task = one complete cycle

**RuboCop as Quality Gate**
- ✅ Auto-fix violations automatically (`rubocop -A`)
- ✅ Understand and fix remaining violations manually
- 🚫 NEVER add `# rubocop:disable` comments
- 🚫 NEVER commit with RuboCop violations

### Absolutely Forbidden

- 🚫 Committing with RuboCop violations
- 🚫 Adding `# rubocop:disable` comments
- 🚫 Writing fake/trivial tests
- 🚫 Lowering coverage thresholds
- 🚫 Large, multi-function changes per commit
- 🚫 Skipping [TODO-INFRASTRUCTURE-*] markers — MUST resolve before proceeding
- 🚫 Batching test problems for later — Record as [TODO-INFRASTRUCTURE-*] and resolve in TDD immediately

### When to Ask User

**MUST ask in these scenarios**:
1. Refactoring direction unclear (how to split method?)
2. Test strategy controversial (what should we test?)
3. Trade-off between simplicity and completeness
4. RuboCop violation needs architectural decision
5. [TODO-INFRASTRUCTURE-*] marker requires design decision

See `.claude/docs/testing-guidelines.md` for detailed examples.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bash0c7) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
