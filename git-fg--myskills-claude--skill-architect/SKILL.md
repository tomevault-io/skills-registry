---
name: skill-architect
description: Creates, refines, ingests, and evaluates agent skills using archetype-driven design. Use when building skills from scratch, extracting knowledge from documentation, or auditing skill quality. Do not use for non-skill related tasks or when other specialized skills are more appropriate.
metadata:
  author: git-fg
---

# Skill Architect

Comprehensive reference for designing, building, ingesting, and evaluating agent skills.

> This skill serves as a **knowledge hub** — all principles, archetypes, and workflows are catalogued here. Load specific workflow references when needed.

> **⚠️ MANDATORY PREREQUISITE**: Before proceeding with ANY task, you MUST read [references/common.md](references/common.md) completely. This contains the foundational principles, archetypes, and shared patterns that all workflows depend on. **NEVER skip this step.**

---

## ⚠️ Required Reading Before Any Task

**MANDATORY — READ ENTIRE FILE**: Before proceeding with any workflow, you MUST read [`references/common.md`](references/common.md) (~370 lines) completely from start to finish. **NEVER skip this prerequisite.**

**Do NOT load** workflow-specific files until you have completed common.md.

---

## What is a Skill?

A Skill is NOT a tutorial. A Skill is a **knowledge externalization mechanism**.

Traditional AI knowledge is locked in model parameters. To teach new capabilities:
```
Traditional: Collect data → GPU cluster → Train → Deploy new version
Cost: $10,000 - $1,000,000+
Timeline: Weeks to months
```

Skills change this:
```
Skill: Edit SKILL.md → Save → Takes effect on next invocation
Cost: $0
Timeline: Instant
```

This is the paradigm shift from "training AI" to "educating AI" — like a hot-swappable LoRA adapter that requires no training.

### The Core Formula

> **Good Skill = Expert-only Knowledge − What Claude Already Knows**

A Skill's value is measured by its **knowledge delta** — the gap between what it provides and what the model already knows.

- **Expert-only knowledge**: Decision trees, trade-offs, edge cases, anti-patterns, domain-specific thinking frameworks
- **What Claude already knows**: Basic concepts, standard library usage, common programming patterns

### The Delta Standard

Only provide context the agent cannot infer.

**Negative**: Explaining what PDFs are or how `import` works
**Positive**: Specific column mappings for proprietary formats

---

## Decision Tree: Which Workflow?

> **⚠️ STOP**: Have you read [`references/common.md`](references/common.md) yet? If not, **read it first**. All workflows depend on understanding these foundational principles.

Use this decision tree to determine which workflow reference to load:

```
START: What do you need to do with skills?
│
├─ "Create a new skill from scratch"
│  └─→ First: [references/common.md](references/common.md) → Then: [references/build.md](references/build.md)
│
├─ "Extract knowledge from docs/skills to create skill"
│  └─→ First: [references/common.md](references/common.md) → Then: [references/ingest.md](references/ingest.md)
│
├─ "Evaluate or audit skill quality"
│  └─→ First: [references/common.md](references/common.md) → Then: [references/judge.md](references/judge.md)
│
└─ "Understand skill principles/archetypes"
   └─→ [references/common.md](references/common.md)
```

**Quick Reference by Task:**

| Your Task | Prerequisite | Load This Reference |
|-----------|-------------|-------------------|
| Build skill from scratch | **Required** | [common.md](references/common.md) → [build.md](references/build.md) |
| Improve/refine existing skill | **Required** | [common.md](references/common.md) → [build.md](references/build.md) |
| Extract from documentation | **Required** | [common.md](references/common.md) → [ingest.md](references/ingest.md) |
| Convert existing skill content | **Required** | [common.md](references/common.md) → [ingest.md](references/ingest.md) |
| Score/evaluate skill quality | **Required** | [common.md](references/common.md) → [judge.md](references/judge.md) |
| Audit skill against best practices | **Required** | [common.md](references/common.md) → [judge.md](references/judge.md) |
| Learn core principles | — | [common.md](references/common.md) |
| Understand archetypes | — | [common.md](references/common.md) |

---

## Reference Files

### [common.md](references/common.md)
**Foundation knowledge** — Core philosophy, principles, archetypes, and shared patterns.

**Contents:**
- Three Core Principles
- The Five Skill Archetypes
- Progressive Disclosure
- Frontmatter Specification
- Black Box Scripts Pattern
- Plan-Validate-Execute Pattern
- Structural Archetypes

### [build.md](references/build.md)
**Creation workflow** — Step-by-step guidance for building and refining skills from scratch.

**Contents:**
- Creating Skills Workflow (8 steps)
- Refining Skills Workflow
- State Anchoring Strategy
- Pipeline Sequencing
- Writing Style Requirements
- Building-specific validation checklists

### [ingest.md](references/ingest.md)
**Extraction workflow** — Golden Path method for distilling knowledge from documentation and existing skills.

**Contents:**
- What Gets Ingested (source types)
- Golden Path Method (8 steps)
- Pattern Extraction
- Source-specific strategies (README, Tutorial, Knowledge Base, etc.)
- Ingestion-specific validation

### [judge.md](references/judge.md)
**Evaluation framework** — 11-dimensional scoring system for comprehensive skill quality assessment.

**Contents:**
- Evaluation Framework (150 points)
- 11 Dimensions with scoring rubrics
- Evaluation Protocol (5 steps)
- Common Failure Patterns
- Practical Examples

---

## Quick Reference

### The Five Skill Archetypes

Each archetype is a **feeling**, not a checklist.

| Archetype | Value | When to Use |
|-----------|-------|-------------|
| **Executor** | Reliability | "I need this done the same way, every time" |
| **Consultant** | Wisdom | "I need expert judgment" |
| **Generator** | Consistency | "Everything must look the same" |
| **Orchestrator** | Coordination | "Many parts, one symphony" |
| **Minimalist** | Simplicity | "Keep it simple" (3 steps or fewer) |

**Vibe Test:**
- Reliable execution needed? → **Executor**
- Expertise and judgment needed? → **Consultant**
- Consistent output from templates? → **Generator**
- Multiple workflows to coordinate? → **Orchestrator**
- Simple and direct? → **Minimalist**

### Three Core Principles

1. **Simplicity Over Fragmentation** — Keep core knowledge in SKILL.md; only disperse when >35,000 characters or highly situational (<5% of tasks)

2. **Set Appropriate Degrees of Freedom** — Match specificity to task fragility. Narrow bridge (low freedom) for fragile operations; open field (high freedom) for variable tasks

3. **Match Autonomy to Task** — Use Criticality + Variability framework: High+Low=Protocol, High+High=Guided, Low+Any=Heuristic

---

## Progressive Disclosure

Skills use tiered loading for optimal token economics:

**Tier 1: Metadata** (~100 tokens)
- Name and description
- Always loaded for discovery

**Tier 2: SKILL.md** (<35,000 characters)
- Complete operational instructions
- Loaded when skill is invoked

**Tier 3: Resources** (as needed)
- Files in references/ or scripts/
- Loaded on specific demand only

---

## Anti-Patterns to Avoid

**Development Anti-Patterns:**
- **Deep Nesting** — `references/v1/setup/config.md` → Flatten to `references/setup-config.md`
- **Vague Description** — "Helps with coding" → Use clear capability + use case
- **Over-Engineering** — Scripts for simple tasks → Use native tools first
- **Code Blocks in Body** — Large code blocks in SKILL.md → Move to examples/ or scripts/

**Archetype Anti-Patterns:**
- **The "Kitchen Sink"** — One skill tries to do everything
- **The "Indecisive Orchestrator"** — Too many paths, unclear direction
- **The "Pretend Executor"** — Scripts that require constant guidance
- **The "Argumentative Consultant"** — Opinions without expertise

---

## When in Doubt

> **⚠️ Remember**: Always read [`references/common.md`](references/common.md) FIRST before any workflow.

1. **Building from scratch?** → [common.md](references/common.md) → [build.md](references/build.md)
2. **Extracting from docs?** → [common.md](references/common.md) → [ingest.md](references/ingest.md)
3. **Evaluating quality?** → [common.md](references/common.md) → [judge.md](references/judge.md)
4. **Learning fundamentals?** → [common.md](references/common.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/git-fg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
