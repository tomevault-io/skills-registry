---
name: skill-farm
description: Skill lifecycle management. Invoke when: creating new skills, improving existing skills, task mentions 'skill improvement', editing SKILL.md files, or running AX Use when this capability is needed.
metadata:
  author: demithras
---

# SKILL-FARMING — Cultivate Your Skills

> **Grow skills. Harvest artifacts. Stay CLEAR.**

## Philosophy

**The Farm Metaphor** *(model)*:
- **Plant** = CREATE (seed a new skill)
- **Grow** = IMPROVE (nurture through usage and feedback)
- **Harvest** = The artifacts produced (committed code, persisted decisions)
- **Clear the field** = Session close (`/clear`) — safe because harvest is complete

**Core Question**: Every skill answers — **"Are we CLEAR?"**
1. **CLEAR Conformance** — Did we follow the META CLEAR framework?
2. **Session Safety** — Is all work persisted? Safe to run `/clear`?

**Epistemic note**: Claims marked with *(fact/model/heuristic/belief)* indicate confidence level.

---

## AX Design Principles (Primary Focus)

**AX (Agent Experience)** is how AI agents experience skills — distinct from UX (User Experience) which focuses on humans. *(fact — defined in /meta)*

**AX is the PRIMARY audience for skills.** *(model)* Skills are consumed by agents far more often than directly by humans. Optimize for agent parseability first.

### Two Dimensions of AX

| Dimension | What It Means | Examples |
|-----------|---------------|----------|
| **Prompt Format (Input)** | How agents invoke the skill | Clear syntax, explicit flags, deterministic parsing |
| **Output Format (Result)** | How agents consume skill output | Structured blocks, extractable state, no implicit meaning |

### AX Principles for All Skills

| Principle | Description | Validation Question |
|-----------|-------------|---------------------|
| **Deterministic Logic** | Same input → same behavior | Can another agent execute this identically? |
| **Explicit State** | Machine-parseable output | Is there a structured block agents can extract? |
| **No Implicit Knowledge** | Spell out what humans would intuit | Would this work without cultural context? |
| **Structured Output** | Tables > prose | Can key data be parsed without NLP? |
| **Recovery Paths** | Clear error handling | What does the agent do when X fails? |
| **Layered Output** | Human-readable + agent-parseable | Does output serve both UX and AX? |

**UX ≠ AX** *(model)*: What works for humans (scanning, inference) often fails for agents (need parsing, explicit logic). Design for AX first, then add UX polish.

### Dual Audience Output Pattern

Skills should produce **layered output**:

1. **Visible layer (UX)**: Compact, scannable for humans
2. **Hidden layer (AX)**: Structured state for agent continuity

```
<!-- skill:state
field: value
status: {phase: done, next: action}
/skill:state -->
```

**See**: `/meta/patterns/ux-ax-dual-audience-output-2026-01-23.yaml`

---

## Skill Hierarchy

`/skill-farm` is the **meta-layer** — it manages the lifecycle of ALL skills, including `/meta` itself.

```
/meta (Layer 0: Framework)     ←── defines CLEAR
  ├── /skill-check (Layer 1)   ←── quality validation
  ├── /next (Layer 1)          ←── work tracking
  ├── /retro (Layer 1)         ←── captures learnings
  └── /skill-farm (Layer 2)    ←── manages all layers
          └── can IMPROVE /meta (recursive but bounded)
```

### Skill Focusing Principles *(model)*

- Focused skill has **one responsibility** and **one primary verb**
- A focused skill must **produce or verify a single concrete artifact**
- Focused skill concerns about closure (CLEAR Check) of **its own scope only**
- A focused skill is **independent** of other skills:
  - Safe to execute in any order
  - Doesn't orchestrate or call other skills
- A focused skill **reduces uncertainty in one dimension only**
- A focused skill implements **CLEAR META** for its domain:
  - Each CLEAR phase executed in its **most effective form**
  - Not just "has phases" but "optimizes each phase for this domain"

**Recursive relationship** *(model)*: `/skill-farm` can improve `/meta`, but uses CLEAR methodology (from `/meta`) to do so. This is safe because:
1. Exit criteria are explicit (version bumped, trigger addressed, `/retro` run)
2. CLEAR provides bounded reflection (E prevents infinite analysis)
3. Git versioning allows rollback

**See**: `/meta/references/skill-hierarchy.md` for the complete hierarchy.

---

## Quick Start

```
/skill-farm create my-skill    # Plant a new skill
/skill-farm improve my-skill   # Grow an existing skill
/skill-farm assess my-skill    # Evaluate AX quality
```

---

## Commands

| Command | Purpose |
|---------|---------|
| `/skill-farm create <name>` | Birth a new skill |
| `/skill-farm improve <skill>` | Grow an existing skill |
| `/skill-farm assess <skill>` | Evaluate skill's AX quality |
| `/skill-farm branch <skill>` | Create project-specific branch of global skill |

---

## CREATE

**Syntax**: `/skill-farm create <name> [--beads]`

**Process**:

**C — Clarity**: Gather context
1. Validate name (kebab-case, no conflicts) *(fact — deterministic check)*
2. Ask: purpose, tools needed, scope (global/project)
3. Check Pattern Library: `/meta/patterns/` for process patterns *(heuristic)*

**L — Legitimacy**: Validate CLEAR META + AX conformance *(model — ensures focus-skill quality)*
4. Ask: Which CLEAR phase(s) does this skill specialize in?
5. Validate: How does it execute that phase in the **most effective form** for this domain?
   - If no clear answer → skill is too generic, needs tighter focus
   - Example: `/meta --plan` executes E-phase most effectively via criterion → task expansion
6. **AX Validation** *(required)*: Verify AX principles will be met:
   | Check | Question |
   |-------|----------|
   | Deterministic? | Can another agent execute identically? |
   | Explicit State? | Will output include structured block? |
   | No Implicit? | Works without cultural context? |
   | Recovery? | What happens on failure? |

**A — Action**: Build the skill
7. Create `<skill-name>/skill.md` with:
   - Clear syntax (AX: Prompt Format)
   - Structured output template (AX: Output Format)
   - Agent Execution Guide if complex
   - Hidden state block template: `<!-- skill:state ... -->`
8. Create `<skill-name>/tests.json` with basic test scenarios *(fact — file creation)*
9. If `--beads`: create tracking issue + validation issue

**E — Exit**: ✓ **Done when**: skill.md exists with AX sections, tests.json exists, skill invokes without error *(fact — verifiable criteria)*

**Output**: A working skill optimized for AX. *(fact)* Use it immediately. Improve as you learn. *(heuristic)*

---

## IMPROVE

**Syntax**: `/skill-farm improve <skill>`

**Triggers** *(heuristic)*:
- After using → reflection reveals gaps
- When it fails → errors signal need
- **AX Gap** → skill works for humans but not agents *(new trigger)*

**Process**:

**C — Clarity**: Understand current state
1. Read current skill *(fact — file read)*
2. Ask: What's the improvement? What triggered it?
3. Check Pattern Library: `/meta/patterns/` (process) + `<skill>/patterns/` (domain) *(heuristic)*
4. **AX Assessment** *(required for all improvements)*:
   ```
   AX Scorecard:
   [ ] Deterministic Logic — same input → same behavior?
   [ ] Explicit State — structured output block exists?
   [ ] No Implicit Knowledge — works without cultural context?
   [ ] Structured Output — tables > prose?
   [ ] Recovery Paths — error handling defined?
   [ ] Dual Audience — serves both UX and AX?
   ```
   *Note: If < 4 checks pass, consider AX improvement as part of this change.*

**L — Legitimacy**: Validate the change
5. Determine scope *(model — judgment call)*:
   | Scope | Workflow |
   |-------|----------|
   | **Small** | Direct edit |
   | **Medium** | Discussion → edit |
   | **Large** | Plan → implement |

**A — Action**: Execute the change
6. Execute, bump version *(fact — observable changes)*
7. If AX scorecard showed gaps → address them in this change

**R — Review**: Close the learning loop *(required)*
8. Run `/retro` to capture learnings

**E — Exit**: ✓ **Done when**: change addresses trigger, version bumped, AX scorecard reviewed, AND `/retro` completed *(fact — verifiable criteria)*

**R-META enforcement**: IMPROVE is not complete until `/retro` captures learnings. *(model — learning loops require closure to be effective)*

**Retro prompts**:
- **What triggered this?** (the friction/failure)
- **What's reusable?** (pattern for other skills)
- **AX impact?** (did this improve agent experience?)

---

## ASSESS

**Syntax**: `/skill-farm assess <skill>` or `/skill-farm assess AX of <skill>`

**Purpose**: Evaluate a skill's Agent Experience (AX) quality using the 6-principle AX Scorecard. Use when auditing skills without making changes.

**Process**:

1. Read the target skill's SKILL.md *(fact — file read)*
2. Evaluate against each AX principle:

| # | Principle | What to Check |
|---|-----------|---------------|
| 1 | **Deterministic Logic** | Are steps explicit? Same input → same behavior? |
| 2 | **Explicit State** | Does output include `<!-- skill:state -->` block? |
| 3 | **No Implicit Knowledge** | Are all terms defined? Acronyms spelled out? |
| 4 | **Structured Output** | Tables for key data? Minimal prose? |
| 5 | **Recovery Paths** | Error handling defined? Fallback logic? |
| 6 | **Dual Audience** | Both human-readable AND agent-parseable layers? |

3. Output scorecard with pass/fail per principle
4. Recommend improvements if score < 5/6

**Output format**:

```
## AX Assessment: <skill-name>

| Principle | Status | Notes |
|-----------|--------|-------|
| Deterministic Logic | PASS/FAIL | [observation] |
| Explicit State | PASS/FAIL | [observation] |
| No Implicit Knowledge | PASS/FAIL | [observation] |
| Structured Output | PASS/FAIL | [observation] |
| Recovery Paths | PASS/FAIL | [observation] |
| Dual Audience | PASS/FAIL | [observation] |

**Score**: X/6 — [Excellent/Good/Acceptable/Poor] AX

**Recommendations**: [if score < 5/6]
```

**Exit**: ✓ **Done when**: scorecard completed with pass/fail for all 6 principles *(fact — verifiable)*

---

## BRANCH

**Syntax**: `/skill-farm branch <skill>`

**Purpose**: Create a project-specific branch of a global skill for customization.

**When to branch** *(heuristic)*:
- Project needs different prompts/behavior than global
- Want to experiment without affecting global skill
- Project-specific integrations required

**Process**:

**C — Clarity**: Verify branching context
1. Locate global skill: `~/.claude/skills/<skill>/SKILL.md` *(fact — file exists check)*
2. Check no project branch exists: `.claude/skills/<skill>/SKILL.md`
3. Ask: Why branch? What will be customized?

**A — Action**: Create branch
4. Copy global skill to project: `cp -r ~/.claude/skills/<skill> .claude/skills/<skill>`
5. Update version to indicate branch: `X.Y.Z` → `X.Y.Z+1-project`
6. Add branch note to history:
   ```yaml
   history:
     - version: "X.Y.Z+1-project"
       date: "YYYY-MM-DD"
       changes: "Branched from global for: [reason]"
   ```

**E — Exit**: ✓ **Done when**: project skill exists, version indicates branch *(fact — verifiable)*

**Output**:
```
✅ Branched: /skill → project scope

Global: ~/.claude/skills/skill/SKILL.md (v1.0.0)
Project: .claude/skills/skill/SKILL.md (v1.0.1-project)

Project version will now override global.
Use `/list-skills` to verify.
```

**Note**: See [[architecture/skill-branching]] for full branching workflow.

---

## Agent Execution Guide

For AI agents executing this skill, follow this deterministic logic:

```
1. PARSE command:
   IF input matches "create <name>" → action = CREATE
   IF input matches "improve <skill>" → action = IMPROVE
   IF input matches "assess <skill>" → action = ASSESS
   IF input matches "branch <skill>" → action = BRANCH
   ELSE → ERROR "Unknown command. Use: create, improve, assess, or branch"

2. EXECUTE based on action:

   ═══════════════════════════════════════════════════════════════
   CREATE:
   ═══════════════════════════════════════════════════════════════

   2.1 VALIDATE name:
       IF name not kebab-case → ERROR "Name must be kebab-case"
       IF Glob(".claude/skills/{name}/SKILL.md") exists → ERROR "Skill already exists"

   2.2 CHECK Pattern Library (REQUIRED):
       patterns = Glob(".claude/skills/meta/patterns/*.yaml")
       relevant = FILTER patterns WHERE filename contains "skill-" OR "ax-" OR "ux-"
       FOR EACH pattern IN relevant (limit 5 most recent):
         Read pattern
         EXTRACT key_insight, decision.chosen
       OUTPUT "Patterns consulted: [list]"

   2.3 ASK purpose and tools:
       AskUserQuestion: purpose, allowed-tools, CLEAR phase specialization

   2.4 VALIDATE AX principles will be met:
       FOR EACH principle IN [Deterministic, ExplicitState, NoImplicit, Structured, Recovery, DualAudience]:
         IF principle cannot be satisfied → WARN and ask for mitigation

   2.5 CREATE files:
       Write SKILL.md with required sections
       Write tests.json with minimum 4 tests

   2.6 VALIDATE required sections exist in created SKILL.md:
       required_sections = ["## CLEAR Check", "<!-- .*:state"]
       FOR EACH section IN required_sections:
         IF section NOT IN created SKILL.md:
           ERROR "Missing required section: {section}"
           FIX by adding section before continuing

   2.7 OUTPUT CLEAR Check

   ═══════════════════════════════════════════════════════════════
   IMPROVE:
   ═══════════════════════════════════════════════════════════════

   2.1 READ current skill:
       skill_path = Glob(".claude/skills/{skill}/SKILL.md")
       IF not exists → ERROR "Skill not found"
       Read skill_path

   2.2 ASK improvement details:
       AskUserQuestion: what's the improvement? what triggered it?

   2.3 ASSESS current AX score:
       Run AX Scorecard (6 principles)
       IF score < 4 → WARN "Consider AX improvement as part of this change"

   2.4 DETERMINE scope and execute:
       IF small change → direct edit
       IF medium change → discuss then edit
       IF large change → plan then implement

   2.5 BUMP version in frontmatter

   2.6 VALIDATE required sections exist:
       IF "## CLEAR Check" NOT IN skill:
         WARN "Missing CLEAR Check section"
         ADD section using template

   2.7 PROMPT for /retro (R-phase required)

   2.8 OUTPUT CLEAR Check

   ═══════════════════════════════════════════════════════════════
   ASSESS:
   ═══════════════════════════════════════════════════════════════

   2.1 READ target skill
   2.2 EVALUATE each of 6 AX principles
   2.3 OUTPUT scorecard with pass/fail
   2.4 IF score < 5 → OUTPUT recommendations

   ═══════════════════════════════════════════════════════════════
   BRANCH:
   ═══════════════════════════════════════════════════════════════

   2.1 CHECK global skill exists:
       global_path = "~/.claude/skills/{skill}/SKILL.md"
       IF NOT EXISTS(global_path) → ERROR "Global skill not found: {skill}"

   2.2 CHECK project branch does not exist:
       project_path = ".claude/skills/{skill}/SKILL.md"
       IF EXISTS(project_path) → ERROR "Project branch already exists"

   2.3 ASK branching reason:
       AskUserQuestion: Why branch? What customization needed?

   2.4 COPY global to project:
       Bash("mkdir -p .claude/skills/{skill}")
       Bash("cp -r ~/.claude/skills/{skill}/* .claude/skills/{skill}/")

   2.5 UPDATE version:
       current_version = EXTRACT version from SKILL.md
       new_version = INCREMENT_PATCH(current_version) + "-project"
       EDIT frontmatter version field

   2.6 ADD history entry:
       APPEND to history array:
         version: new_version
         date: TODAY
         changes: "Branched from global for: [user's reason]"

   2.7 OUTPUT confirmation with both paths

3. ERROR recovery:
   IF file operation fails → report error, suggest manual fix
   IF AskUserQuestion times out → proceed with defaults, note assumptions
   IF pattern library empty → WARN "No patterns found", proceed without
```

---

## AX Scorecard (Skill Evaluation)

Use this scorecard when creating or improving skills. **Target: 5/6 minimum for production skills.**

| # | Principle | Question | Pass Criteria |
|---|-----------|----------|---------------|
| 1 | **Deterministic Logic** | Same input → same behavior? | Pseudocode or clear steps provided |
| 2 | **Explicit State** | Structured output for agents? | `<!-- skill:state -->` block defined |
| 3 | **No Implicit Knowledge** | Works without cultural context? | All terms defined, acronyms spelled out |
| 4 | **Structured Output** | Tables > prose? | Key data in tables, not paragraphs |
| 5 | **Recovery Paths** | Error handling defined? | ERROR section or fallback logic |
| 6 | **Dual Audience** | Serves both UX and AX? | Visible (human) + hidden (agent) layers |

**Scoring**:
- **6/6**: Excellent AX — skill is agent-native
- **5/6**: Good AX — production ready
- **4/6**: Acceptable — improve on next iteration
- **< 4**: Poor AX — requires improvement before release

---

## Frontmatter Schema

```yaml
---
name: string        # kebab-case
version: string     # semver
description: string
allowed-tools: []
lifecycle:
  status: active
history:
  - version: string
    date: date
    changes: string
---
```

---

## Tests Schema

Every skill gets a `tests.json` file defining expected behaviors:

```json
{
  "skill": "skill-name",
  "version": "1.0.0",
  "tests": [
    {
      "id": "unique-test-id",
      "name": "Human-readable test name",
      "input": "/skill-name <args>",
      "precondition": "optional context requirement",
      "expected": {
        "output_contains": ["expected", "strings"],
        "runs_command": "optional shell command",
        "creates_file": "optional file pattern",
        "uses_tool": "optional tool name"
      }
    }
  ]
}
```

**Test categories to cover** *(heuristic)*:
1. **Happy path** — Basic invocation works
2. **Each command variant** — All documented commands tested
3. **Edge cases** — Empty input, missing args, invalid input
4. **Key behaviors** — Core features the skill promises
5. **AX tests** *(required)* — Agent experience validation:
   - Output contains structured state block
   - Deterministic: same input → consistent output structure
   - Error case: produces parseable error state

**Minimum tests**: 4 per skill (happy path + 2 behaviors + 1 AX test)

---

## CLEAR Check (Required Exit)

**Every skill invocation must end with a CLEAR check.** *(model — universal exit pattern)*

### Compact Format (Default)

```
📋 CLEAR: C✓ L✓ E✓ A✓ R○ | AX: 5/6 | Next: [action]
```

### Full Format (When NOT CLEAR)

```
📋 CLEAR: C✓ L✓ E✗ A○ R-
   ↳ E blocked: [reason]
   ⚡ [action to resolve]

🌾 Uncommitted: [files] — run `git add && git commit`
```

### Consider Opening Issues (Conditional)

When ideas, enhancements, or deferred work emerge during skill work, prompt user to persist:

```
📋 CLEAR: C✓ L✓ E✓ A✓ R✓ | AX: 5/6 | Next: session complete

💡 Consider opening issues:
  - [Discovered enhancement for skill X]
  - [Pattern worth extracting to library]
  - [Deferred improvement for future session]
```

**Rules**:
- Only show when actionable future work was discovered
- Tool-agnostic: don't mention specific tracker names (not "create beads", just "open issues")
- User can say "open these" or ignore
- Omit entirely if nothing to suggest
- Common triggers: edge cases found, patterns discovered, scope creep deferred

**Propagation requirement** *(model)*: Skills created by `/skill-farm` should inherit this pattern. See templates for implementation.

### Structured State (for AX continuity)

Every CLEAR Check includes hidden agent state:

```
<!-- skill-farm:state
action: create|improve
target: skill-name
clear: {C: done, L: done, E: pending, A: pending, R: pending}
ax_score: 5/6
next: "specific action"
suggested_issues: ["optional array of discovered work"]
/skill-farm:state -->
```

### R-Phase Validation Rule

**R✓ requires `/retro` to have been invoked** *(fact — guardrail against false closure)*

| Condition | R Status | Rationale |
|-----------|----------|-----------|
| `/retro` was run this session | R✓ | Learning loop closed |
| User explicitly waived (`--no-retro` or "skip retro") | R✓ | Intentional skip |
| Neither of the above | **R○** | Pending — do not mark complete |

**Anti-pattern**: Marking R✓ because "work feels done" — this is completion bias, not learning closure.

**Propagation**: Skills created by `/skill-farm` inherit this rule via templates.

### Implementation

At the end of CREATE or IMPROVE:

1. **Check CLEAR stages** — Did we complete C, L, E, A, R?
2. **Check R-phase** — Was `/retro` invoked? If not, R must be ○
3. **Check AX scorecard** — Score the created/improved skill
4. **Check git status** — Any uncommitted changes?
5. **Check discovered work** — Any ideas/tasks worth persisting?
6. **Output status** — Compact format + optional issues + hidden state block

**When ✅ CLEAR**: User can safely run `/clear`. All work is harvested (persisted).

---

## Skill Lifecycle: Plant → Grow → Harvest

```
┌─────────────────────────────────────────────────────────────┐
│                      SKILL FARMING                          │
│                                                             │
│   🌱 PLANT (CREATE)                                         │
│   └── New skill seeded with SKILL.md + tests.json          │
│                                                             │
│   🌿 GROW (IMPROVE)                                         │
│   └── Skill evolves through usage and feedback             │
│   └── Version bumps track growth                           │
│   └── /retro captures learnings                            │
│                                                             │
│   🌾 HARVEST                                                │
│   └── Artifacts: committed code, persisted decisions       │
│   └── git commit + push = harvest complete                 │
│                                                             │
│   ✅ CLEAR THE FIELD                                        │
│   └── Safe to /clear — nothing lost                        │
└─────────────────────────────────────────────────────────────┘
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/demithras) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
