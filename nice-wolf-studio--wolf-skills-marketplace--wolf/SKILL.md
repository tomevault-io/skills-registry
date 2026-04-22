---
name: wolf
description: Master skill for Wolf Agents institutional knowledge and behavioral patterns (v1.1.0 with skill-chaining) Use when this capability is needed.
metadata:
  author: nice-wolf-studio
---

# Wolf Agents Master Skill

This is the master skill that provides access to Wolf Agents' institutional knowledge accumulated over 50+ phases of development. Enhanced with **Superpowers Skill-Chaining patterns** (Phase 1 & 2) for dramatically improved agent compliance.

## What's New in v1.1.0

**Phase 1 & 2 Enhancements** (November 2025):
- ✅ **Explicit Skill Chaining**: "REQUIRED NEXT SKILL" callouts forcing sequential workflows
- ✅ **Rationalization Blocking**: "Red Flags - STOP" sections catching common shortcuts
- ✅ **Mandatory Verification**: Checklists with pass/fail criteria that cannot be skipped
- ✅ **Good/Bad Examples**: Concrete compliance patterns (8 example pairs added)
- ✅ **Subagent Templates**: 4 role templates for easy delegation
- ✅ **Extended Coverage**: All core skills + wolf-verification integrated

**Expected Impact**: Agent compliance rates improved from 30-50% to 90-95% across all governance requirements.

## Available Wolf Skills

### Core Framework Skills (v1.1.0 - v1.2.0)

#### 🚀 wolf-session-init (v1.0.0) **START HERE**
**Master initialization skill with mandatory 4-step protocol**

**Use when**:
- Starting ANY new work or session
- Session recovery after context loss
- Beginning implementation work

**Provides**:
- BLOCKING gates for principles → archetype → governance → role
- Session initialization checklist
- Context recovery protocol

**MCP Tool**: Start with this skill, then follow its chain

---

#### 🎯 wolf-principles (v1.1.0)
**Wolf's 10 core principles guiding system design and agent behavior**

**Use when**:
- Making architectural decisions
- Justifying design choices
- Resolving conflicts between priorities
- Understanding Wolf's philosophy

**Enhancements**:
- Red Flags - STOP section (6 rationalizations)
- Chains to wolf-archetypes
- Verification checklist (5 items)

**MCP Tool**: `mcp__wolf-knowledge__query_principles({ principle_id: 1-10 })`

---

#### 🔄 wolf-archetypes (v1.2.0)
**Behavioral archetype selection with overlay lenses**

**Use when**:
- Starting new work items
- Determining priorities and evidence requirements
- Applying specialized quality gates (performance, security, accessibility, observability)

**Enhancements**:
- Red Flags - STOP section (7 rationalizations)
- 5 Good/Bad example pairs showing proper archetype selection
- Chains to wolf-governance
- Verification checklist (6 items)

**MCP Tool**: `mcp__wolf-knowledge__find_archetype({ labels: [...], description: "..." })`

---

#### 🛡️ wolf-governance (v1.2.0)
**Compliance rules, quality gates, and process standards**

**Use when**:
- Checking Definition of Done requirements
- Understanding quality gates (Fast-Lane, Full-Suite)
- Validating PR readiness
- Understanding approval requirements

**Enhancements**:
- Red Flags - STOP section (8 rationalizations)
- 3 Good/Bad example pairs (feature PR, security change, refactoring)
- Chains to wolf-roles and wolf-verification
- Verification checklist (8 items)

**MCP Tool**: `mcp__wolf-knowledge__search_governance({ query: "quality gates" })`

---

#### 📋 wolf-roles (v1.2.0)
**Guidance for 50+ specialized agent roles with responsibilities**

**Use when**:
- Understanding role responsibilities and boundaries
- Determining collaboration patterns
- Identifying escalation paths
- Using subagent templates for delegation

**Enhancements**:
- Red Flags - STOP section (7 rationalizations)
- 4 subagent templates (coder, pm, security, code-reviewer)
- Subagent delegation patterns
- Verification checklist (6 items)
- Marks completion of primary skill chain ✅

**MCP Tool**: `mcp__wolf-knowledge__get_role_guidance({ role_name: "agent-role" })`

---

#### ✅ wolf-verification (v1.1.0)
**Three-layer verification (CoVe, HSP, RAG) for continuous validation**

**Use when**:
- During implementation at checkpoints
- Before claiming work complete
- Validating evidence requirements
- Checking confidence scores

**Enhancements**:
- Red Flags - STOP section (7 rationalizations)
- 2 Good/Bad examples
- Integration with governance gates
- Verification checklist (6 items)

**MCP Tool**: `mcp__wolf-core-ip__check_confidence({ model_confidence, evidence_count, ... })`

---

### Automation & Scripts (v1.1.0)

#### ⚙️ wolf-scripts-core (v1.1.0)
**Core automation for archetype selection, evidence validation, quality scoring**

**Use when**:
- Automating archetype selection
- Validating evidence requirements
- Scoring issue quality with curator rubric
- Validating bash scripts

**Enhancements**:
- Red Flags - STOP section (6 rationalizations)
- 2 Good/Bad examples (archetype selection, evidence validation)
- Integration with wolf-archetypes and wolf-governance
- Verification checklist (5 items)

**Scripts**: `select-archetype.mjs`, `evidence-validator.mjs`, `curator-rubric.mjs`, `bash-validator.mjs`

---

#### 🤖 wolf-scripts-agents (v1.1.0)
**Agent coordination, orchestration, and multi-agent workflow management**

**Use when**:
- Coordinating multi-agent workflows
- Enforcing agent file scope boundaries
- Using mailbox system for async communication
- Orchestrating complex pipelines

**Enhancements**:
- Red Flags - STOP section (6 rationalizations)
- 2 Good/Bad examples (workflow orchestration, scope validation)
- Integration with wolf-roles
- Verification checklist (6 items)

**Scripts**: `orchestrate-workflow.mjs`, `validate-agent-changes.mjs`, `agent-executor.mjs`

---

### Supporting Skills (v1.0.0)

#### 📚 wolf-instructions
**Four-level instruction cascading (Global → Domain → Project → Role)**

**Use when**:
- Resolving instruction conflicts
- Understanding priority hierarchy
- Loading contextual guidance

#### 📝 wolf-adr
**Architecture Decision Records system**

**Use when**:
- Documenting architectural decisions
- Understanding past decisions
- Creating ADRs for major changes

## Complete Skill Chain Diagram

```
SESSION START
    |
    v
[wolf-session-init] - MANDATORY ENTRY POINT
    |  Step 1: Query principles (BLOCKING)
    |  Step 2: Find archetype (BLOCKING)
    |  Step 3: Load governance (BLOCKING)
    |  Step 4: Load role (BLOCKING)
    v
PRIMARY SKILL CHAIN (Sequential - DO NOT skip)
    |
    v
[wolf-principles] (v1.1.0)
    |  → Strategic guidance and decision framework
    |  → REQUIRED NEXT: wolf-archetypes
    v
[wolf-archetypes] (v1.2.0)
    |  → Behavioral profile and evidence requirements
    |  → Apply lenses if needed (performance, security, accessibility, observability)
    |  → REQUIRED NEXT: wolf-governance
    v
[wolf-governance] (v1.2.0)
    |  → Definition of Done, quality gates, compliance
    |  → REQUIRED NEXT: wolf-roles
    |  → REQUIRED ALWAYS: wolf-verification
    v
[wolf-roles] (v1.2.0)
    |  → Role responsibilities, collaboration patterns
    |  → Use subagent templates for delegation
    |  → PRIMARY CHAIN COMPLETE ✅
    v
IMPLEMENTATION BEGINS
    |
    v
[wolf-verification] (v1.1.0) - Called DURING work at checkpoints
    |  → Three-layer validation (CoVe, HSP, RAG)
    |  → Evidence collection
    |  → Confidence scoring
    v
COMPLETION ✅
```

## When to Use Each Skill - Decision Tree

```
START: Are you beginning new work or recovering context?
│
├─ YES → Use wolf-session-init (MANDATORY)
│         └─ Follow its 4-step blocking protocol
│
└─ NO: What do you need?
    │
    ├─ "Strategic guidance / decision framework"
    │   └─ Use wolf-principles
    │       └─ MCP: mcp__wolf-knowledge__query_principles
    │
    ├─ "What archetype for this work type?"
    │   └─ Use wolf-archetypes
    │       └─ MCP: mcp__wolf-knowledge__find_archetype
    │       └─ OR Script: select-archetype.mjs
    │
    ├─ "What are quality gates / Definition of Done?"
    │   └─ Use wolf-governance
    │       └─ MCP: mcp__wolf-knowledge__search_governance
    │
    ├─ "What are my role responsibilities?"
    │   └─ Use wolf-roles
    │       └─ MCP: mcp__wolf-knowledge__get_role_guidance
    │       └─ Use templates for subagent delegation
    │
    ├─ "How do I validate evidence / check confidence?"
    │   └─ Use wolf-verification
    │       └─ MCP: mcp__wolf-core-ip__check_confidence
    │
    ├─ "Need to automate archetype selection or evidence validation?"
    │   └─ Use wolf-scripts-core
    │       └─ Scripts: select-archetype.mjs, evidence-validator.mjs
    │
    ├─ "Need to coordinate multiple agents?"
    │   └─ Use wolf-scripts-agents
    │       └─ Scripts: orchestrate-workflow.mjs, validate-agent-changes.mjs
    │
    ├─ "Need to understand instruction priority?"
    │   └─ Use wolf-instructions
    │
    └─ "Need to create ADR?"
        └─ Use wolf-adr
```

## Red Flags - STOP

If you catch yourself thinking:

- ❌ **"I don't need the master skill, I know Wolf"** - STOP. Master skill is updated with Phase 1 & 2 enhancements. Skills evolve. Read current version.
- ❌ **"Master skills are just documentation"** - NO. Master skill coordinates the entire framework. It shows skill chain and decision tree.
- ❌ **"I can skip wolf-session-init and start coding"** - FORBIDDEN. wolf-session-init is MANDATORY entry point with blocking gates.
- ❌ **"I'll just use wolf-principles and skip the rest"** - Wrong. Principles alone don't provide archetype, governance, or role context. Follow the chain.
- ❌ **"The old workflow still works"** - False. Phase 1 & 2 added blocking gates. Old workflow had 60-70% skip rates. New workflow enforces compliance.

**STOP. Use the complete skill chain starting with wolf-session-init.**

## After Using This Skill

**REQUIRED NEXT STEPS:**

```
Master skill is a reference - not part of workflow chain
```

1. **If starting new work**: Use **wolf-session-init** as entry point
   - **Why**: wolf-session-init is MANDATORY entry point with 4-step blocking protocol
   - **Gate**: Cannot proceed to implementation without completing wolf-session-init
   - **This skill (wolf)**: Provides overview and decision tree, but wolf-session-init starts the actual workflow

2. **If looking for specific guidance**: Use decision tree above
   - Navigate to appropriate skill based on need
   - Follow MCP tool calls or script usage as documented
   - Follow skill chains (each skill points to next required skill)

3. **Return to this skill**: When you need skill discovery or decision tree
   - This is a reference/catalog skill
   - Not part of mandatory workflow
   - Use for navigation and overview

### Common Workflows

#### Starting New Work (ALWAYS)
```
1. Use wolf-session-init (MANDATORY)
2. Follow blocking gates: principles → archetype → governance → role
3. Begin implementation with complete context
4. Use wolf-verification at checkpoints
```

#### Understanding a Specific Topic
```
1. Use decision tree above to find relevant skill
2. Read that skill for detailed guidance
3. Follow its "After Using This Skill" chain if applicable
```

#### Delegating to Subagent
```
1. Load wolf-roles for role guidance
2. Select appropriate role template (coder, pm, security, code-reviewer)
3. Fill placeholders with task details
4. Use Task tool to dispatch subagent with template
```

## Integration Points

Wolf skills integrate with:
- **GitHub**: Labels trigger archetype selection, PR workflows enforce governance
- **MCP Tools**: All skills have corresponding MCP tool for querying Wolf knowledge
- **Scripts**: Automation scripts in wolf-scripts-core and wolf-scripts-agents
- **Templates**: Role templates in wolf-roles for subagent delegation
- **ADRs**: wolf-adr for documenting architectural decisions
- **Journals**: Required by governance for all work

## Performance Benefits

Compared to MCP servers:
- **50x faster** load times (<10ms vs 500ms)
- **40x fewer tokens** (50 vs 2000)
- **Zero memory overhead** (on-demand vs 50MB process)
- **Auto-composition** based on context
- **Enhanced with skill-chaining**: Blocking gates enforce workflow compliance

---

**Last Updated**: 2025-11-14
**Phase**: Superpowers Skill-Chaining Enhancement v2.0.0 (Phase 3 in progress)
**Master Skill**: Coordinates access to Wolf institutional knowledge

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nice-wolf-studio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
