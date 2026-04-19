---
name: workflow-optimization
description: Claude Code artifact optimization patterns and validation. Use when creating commands, skills, agents, hooks, or CLAUDE.md files. Provides optimization rules, anti-patterns, and validation checklists. Use when this capability is needed.
metadata:
  author: mit17k
---

# Workflow Optimization Knowledge Base

## When to Use This Skill

- Creating any Claude Code artifact
- Optimizing existing artifacts
- Validating artifact quality
- Reviewing workflow configurations

## Core Optimization Principles

### 1. Minimal Signal Principle

**Goal:** Smallest possible set of high-signal tokens that maximize desired behavior.

**Token Budgets:**
| Artifact | Target | Maximum |
|----------|--------|---------|
| Command (simple) | <100 tokens | 150 |
| Command (complex) | <300 tokens | 500 |
| Skill description | <200 chars | 1024 chars |
| CLAUDE.md | <1000 tokens | 1500 |
| Agent definition | <500 tokens | 800 |

**Test Each Line:**
1. Can Claude infer this from code? → Remove
2. Does this change Claude's behavior? → Keep if yes
3. Is this project-specific? → Keep if yes
4. Will this become stale? → Reference external doc instead

### 2. Attention Budget Optimization

**"Lost in the Middle" Effect:** Models pay less attention to content in the middle of long contexts.

**Placement Strategy:**
```
┌─────────────────────────┐
│ CRITICAL RULES (first)  │ ← Maximum attention
├─────────────────────────┤
│ Details & examples      │ ← Moderate attention
├─────────────────────────┤
│ Edge cases              │ ← Lower attention
├─────────────────────────┤
│ CRITICAL RULES (repeat) │ ← Attention rises at end
└─────────────────────────┘
```

**Bookend Pattern:** Repeat critical instructions at both START and END of artifact.

### 3. Specificity Over Generality

**❌ Vague (Low Value):**
```
Follow best practices for error handling.
```

**✅ Specific (High Value):**
```
Error handling:
- Catch specific exceptions (never bare `except:`)
- Log with context: logger.error("Failed to X", {userId, error})
- Re-raise after logging unless recoverable
- Pattern: src/core/errors.py
```

### 4. Action-Oriented Framing

**❌ Descriptive (Passive):**
```
Tests should be written before merging.
```

**✅ Imperative (Active):**
```
Run `npm test` and verify all pass before committing.
```

## Optimization Patterns by Artifact Type

### Commands

**Frontmatter Optimization:**
```yaml
---
allowed-tools: [Minimal set needed - principle of least privilege]
argument-hint: [Clear format, e.g., "[file] [--flag]"]
description: [<80 chars, verb phrase: "Generate X for Y"]
---
```

**Body Structure:**
```markdown
# [Purpose - 3-5 words]

$ARGUMENTS contains: [exact format expected]

## Process
1. [Step] - [brief instruction]
2. [Step] - [brief instruction]

## Output
[Exact format specification]

## Constraints
- [Non-negotiable rule]
- [Non-negotiable rule]
```

**Tool Scoping Patterns:**
```
Read-only analysis: Read, Glob, Grep
Code generation: Read, Write, Glob, Grep
Code modification: Read, Write, Edit, MultiEdit, Glob, Grep
Limited shell: Bash(npm:*), Bash(git:*)
Full shell: Bash (use sparingly)
```

### Skills

**Description Formula:**
```
[What] + [Domain/Technology] + "Use when" + [Triggers]
```

**Example:**
```
PostgreSQL query optimization patterns. Use when writing complex queries, 
debugging slow queries, or designing schemas for PostgreSQL databases.
```

**Structure:**
```markdown
# [Skill Name]

## When to Use
- [Explicit trigger 1]
- [Explicit trigger 2]

## Patterns
### [Pattern Name]
[Example with explanation]

### Anti-Pattern: [Name]
[Bad example]
Why: [Reasoning]
```

### Agents

**Role Definition:**
```markdown
## Identity
You are a [specific title] with [years] experience in [domain].
Your expertise: [bullet list of 3-4 specific skills]

## Mission
[Single sentence: what you do and why]
```

**Tool Scoping by Agent Type:**
| Type | Recommended Tools |
|------|-------------------|
| Reviewer | Read, Glob, Grep |
| Generator | Read, Write, Glob, Grep |
| Analyzer | Read, Glob, Grep, Bash(analysis commands) |

### CLAUDE.md

**Priority Order:**
1. Commands (developers use these daily)
2. Warnings (prevent costly mistakes)
3. Architecture (context for decisions)
4. Conventions (code consistency)

**Compression Techniques:**
```markdown
# Before (verbose)
## Database
We use PostgreSQL as our primary database. All database operations
should go through the ORM layer using Sequelize. Direct SQL queries
should be avoided except in special circumstances.

# After (compressed)
## Database
PostgreSQL via Sequelize ORM. Direct SQL only in `src/db/raw/`.
```

### Hooks

**Performance Rules:**
- Hooks run on EVERY matching operation
- Keep commands fast (<5 seconds)
- Use `|| true` for non-critical hooks
- Avoid network calls in hooks

**Common Patterns:**
```json
// Auto-format (most common)
{"type": "command", "command": "prettier --write $CLAUDE_FILE_PATHS || true"}

// Lint check
{"type": "command", "command": "eslint $CLAUDE_FILE_PATHS --fix || true"}

// Type check (TypeScript)
{"type": "command", "command": "tsc --noEmit 2>/dev/null || true"}
```

## Anti-Patterns to Avoid

### 1. Prompt Stuffing
```markdown
❌ Including everything "just in case"
✅ Include only what changes behavior
```

### 2. Aspiration Documentation
```markdown
❌ "We're moving toward microservices..."
✅ "Monolith with service layer. Events in src/events/."
```

### 3. Generic Boilerplate
```markdown
❌ "Follow TypeScript best practices"
✅ "strict: true, prefer type over interface, Zod for runtime"
```

### 4. Implicit Context
```markdown
❌ "Use the standard pattern"
✅ "Use pattern from src/services/base.ts"
```

### 5. Conflicting Instructions
```markdown
❌ "Prioritize speed. Prioritize readability. Prioritize security."
✅ "Priority: (1) correctness, (2) security, (3) readability, (4) speed"
```

### 6. Over-Constraint
```markdown
❌ "Never use any external libraries"
✅ "Prefer stdlib; new deps need justification in PR"
```

## Validation Checklists

### Command Validation
- [ ] Description <80 chars
- [ ] Allowed-tools minimally scoped
- [ ] $ARGUMENTS usage documented
- [ ] Output format specified
- [ ] Error cases handled
- [ ] No vague instructions

### Skill Validation
- [ ] Name: lowercase, hyphens, ≤64 chars
- [ ] Description includes "Use when" triggers
- [ ] Description ≤1024 chars
- [ ] Patterns include anti-patterns
- [ ] File size reasonable (<2KB)

### Agent Validation
- [ ] Clear role identity
- [ ] Specific expertise listed
- [ ] Tools minimally scoped
- [ ] Output format defined
- [ ] Constraints specified

### CLAUDE.md Validation
- [ ] Total <1000 tokens
- [ ] Commands table present
- [ ] Warnings section exists
- [ ] No obvious/inferable content
- [ ] References external docs for details

### Hook Validation
- [ ] Timeout specified for long operations
- [ ] Uses `|| true` for non-critical
- [ ] Matcher is specific (not just "Bash")
- [ ] No network calls unless essential

## Workflow Architecture Patterns

### Linear Pipeline
```
Input → Step1 → Step2 → Step3 → Output
```
Best for: Sequential transformations

### Parallel Fan-Out
```
        ┌→ Agent1 ─┐
Input ──┼→ Agent2 ──┼→ Aggregator → Output
        └→ Agent3 ─┘
```
Best for: Independent analyses

### Conditional Branching
```
Input → Classifier ─┬→ PathA → Output
                    └→ PathB → Output
```
Best for: Type-dependent processing

### Iterative Refinement
```
Input → Generate → Validate ─┬→ Output (if valid)
                             └→ Refine → Validate (loop)
```
Best for: Quality-sensitive outputs

## Metrics for Success

**Optimization Success Indicators:**
- Token usage reduced by 30%+
- First-attempt success rate >80%
- No conflicting instructions
- All artifacts pass validation
- Documentation enables updates

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mit17k) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
