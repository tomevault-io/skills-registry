---
name: research-cascade
description: Multi-source research orchestration. Chains deepwiki, submodules, WebSearch, and codebase search. Defines when to escalate and how to synthesize findings. Use when this capability is needed.
metadata:
  author: creatifcoding
---

# Research Cascade Orchestration

**Purpose**: Define the mechanics of multi-source research. When to use each source, when to escalate, how to synthesize findings.

## Cascade Architecture

```
                         ┌─────────────────────┐
                         │   USER QUESTION     │
                         └──────────┬──────────┘
                                    │
                         ┌──────────▼──────────┐
                         │ UNCERTAINTY CHECK   │
                         │ Am I confident?     │
                         └──────────┬──────────┘
                                    │
                    ┌───────────────┼───────────────┐
                    │ YES           │ NO            │
                    ▼               ▼               │
             ┌──────────┐   ┌──────────────┐       │
             │ PROCEED  │   │ LEVEL 1:     │       │
             │ (rare)   │   │ deepwiki     │       │
             └──────────┘   └──────┬───────┘       │
                                   │               │
                         ┌─────────▼─────────┐     │
                         │ SUFFICIENT?       │     │
                         └─────────┬─────────┘     │
                                   │               │
                    ┌──────────────┼───────────────┤
                    │ YES          │ NO            │
                    ▼              ▼               │
             ┌──────────┐   ┌──────────────┐       │
             │ VERIFIED │   │ LEVEL 2:     │       │
             └──────────┘   │ Submodules   │       │
                            └──────┬───────┘       │
                                   │               │
                         ┌─────────▼─────────┐     │
                         │ SUFFICIENT?       │     │
                         └─────────┬─────────┘     │
                                   │               │
                    ┌──────────────┼───────────────┤
                    │ YES          │ NO            │
                    ▼              ▼               │
             ┌──────────┐   ┌──────────────┐       │
             │ VERIFIED │   │ LEVEL 3:     │       │
             └──────────┘   │ WebSearch    │       │
                            └──────┬───────┘       │
                                   │               │
                         ┌─────────▼─────────┐     │
                         │ SUFFICIENT?       │     │
                         └─────────┬─────────┘     │
                                   │               │
                    ┌──────────────┼───────────────┤
                    │ YES          │ NO            │
                    ▼              ▼               │
             ┌──────────┐   ┌──────────────┐
             │ VERIFIED │   │ [UNCERTAIN]  │
             └──────────┘   │ Admit limits │
                            └──────────────┘
```

---

## Level 1: deepwiki Queries

### When to Use

| Condition | Use deepwiki |
|-----------|--------------|
| Library API question | ✅ Yes |
| Current best practice | ✅ Yes |
| "Has X changed?" | ✅ Yes |
| Implementation details | ⚠️ Maybe (prefer source) |
| TMNL-specific question | ❌ No (use codebase) |

### deepwiki Query Types

**Verification Query** (Preferred):
```
mcp__deepwiki__ask_question
  repoName: "Effect-TS/effect"
  question: "I believe [MY UNDERSTANDING]. Is this correct,
             or has it changed in recent versions?"
```

**Exploration Query**:
```
mcp__deepwiki__ask_question
  repoName: "Effect-TS/effect"
  question: "What are the recommended patterns for [USE CASE]?"
```

**Structure Query** (Map the territory first):
```
mcp__deepwiki__read_wiki_structure
  repoName: "Effect-TS/effect"
```

### Escalation Triggers

Escalate to Level 2 (Submodules) when:
- deepwiki response is vague or incomplete
- Need to see actual code examples
- Want to verify with test patterns
- Response mentions "check documentation for details"

---

## Level 2: Submodule Research

### When to Use

| Condition | Use Submodules |
|-----------|----------------|
| Need code examples | ✅ Yes |
| Verify deepwiki claim | ✅ Yes |
| Test pattern needed | ✅ Yes |
| Human-authored prose | ✅ website submodule |
| Implementation source | ✅ effect submodule |

### Submodule Navigation

**Effect Website (Human Docs)**:
```bash
# List available topics
ls ../../submodules/website/content/src/content/docs/docs/

# Search for topic
find ../../submodules/website -name "*.mdx" | xargs grep -l "TOPIC"

# Read specific doc
cat ../../submodules/website/content/src/content/docs/docs/[category]/[file].mdx
```

**Effect Tests (Canonical Patterns)**:
```bash
# Find test files
find ../../submodules/effect/packages -name "*.test.ts" | head -30

# Search for pattern in tests
grep -r "PATTERN" ../../submodules/effect/packages/*/test/

# Read specific test
cat ../../submodules/effect/packages/sql-sqlite-bun/test/Client.test.ts
```

**effect-atom Tests**:
```bash
# List test files
ls ../../submodules/effect-atom/packages/atom/test/

# Search for pattern
grep -r "PATTERN" ../../submodules/effect-atom/packages/atom/

# Read specific test
cat ../../submodules/effect-atom/packages/atom/test/Atom.test.ts
```

### Escalation Triggers

Escalate to Level 3 (WebSearch) when:
- Submodule version may be outdated
- Question involves very recent changes
- Looking for breaking changes / migration guides
- Community consensus on edge cases

---

## Level 3: WebSearch

### When to Use

| Condition | Use WebSearch |
|-----------|---------------|
| Recent breaking changes | ✅ Yes |
| Version-specific behavior | ✅ Yes |
| Community discussions | ✅ Yes |
| Migration guides | ✅ Yes |
| Core API questions | ❌ Use deepwiki first |

### WebSearch Query Patterns

**Breaking Changes**:
```
WebSearch
  query: "Effect-TS 3.0 breaking changes 2025 migration"
```

**Recent Updates**:
```
WebSearch
  query: "Effect Schema 2025 new features changes"
```

**Community Patterns**:
```
WebSearch
  query: "Effect-TS service pattern best practice 2025"
```

### Verification Required

WebSearch results must be verified against deepwiki or submodules:
- Blog posts may be outdated
- Community answers may be wrong
- Official sources take precedence

---

## Level 4: Codebase Precedent

### When to Use

Always check codebase precedent AFTER external research:
- Confirms pattern works in TMNL context
- Shows integration with other systems
- Reveals TMNL-specific conventions

### Codebase Search Patterns

**Pattern Registry**:
```bash
cat .edin/EFFECT_PATTERNS.md
cat .edin/EFFECT_SERVICE_PATTERNS.md
cat .edin/EFFECT_TESTING_PATTERNS.md
```

**Working Implementations**:
```bash
# Find pattern usage
grep -r "PATTERN" src/lib/*/

# Find specific service implementations
grep -r "Effect.Service" src/lib/*/services/

# Find atom patterns
grep -r "Atom.runtime\|runtimeAtom" src/lib/*/atoms/
```

**Canonical Examples**:
```bash
# Data Manager (service + atoms)
cat src/lib/data-manager/v1/DataManager.ts

# Slider (behavior pattern)
cat src/lib/slider/v1/services/SliderBehavior.ts

# Search Kernel
cat src/lib/data-manager/v1/kernels/SearchKernel.ts
```

---

## Synthesis Protocol

After gathering information from multiple levels:

### 1. Concordance Check

Do all sources agree?

| Sources Agree | Action |
|---------------|--------|
| All agree | High confidence, proceed |
| Mostly agree | Note minor differences |
| Conflict | Investigate, prefer canonical |
| Unclear | Admit uncertainty |

### 2. Source Priority

When sources conflict:

```
1. Effect tests (packages/*/test/) — Most canonical
2. Website submodule — Human-vetted
3. deepwiki — AI-processed but repo-aware
4. WebSearch — Verify against above
5. Codebase — TMNL-specific but may lag
```

### 3. Synthesis Template

```markdown
## Research Synthesis: [TOPIC]

### Sources Consulted
- [x] deepwiki: Effect-TS/effect
- [x] Submodule: website/docs/[topic].mdx
- [x] Submodule: effect/packages/[pkg]/test/[file].test.ts
- [ ] WebSearch: (not needed / consulted)
- [x] Codebase: src/lib/[module]/

### Findings

**deepwiki says**: [Summary]

**Submodule confirms**: [Summary]

**Codebase shows**: [Summary]

### Concordance
[All agree / Minor differences / Conflict requiring resolution]

### Verified Pattern
```typescript
// The verified pattern with high confidence
```

### Confidence Level
[VERIFIED-MULTI] / [VERIFIED-DEEPWIKI] / [INFERRED]
```

---

## Parallel vs Sequential Research

### Parallel (Fast Path)

Use when question is well-understood:

```
┌─────────────┬─────────────┬─────────────┐
│  deepwiki   │  Submodule  │  Codebase   │
│   Query     │   Search    │   Search    │
└──────┬──────┴──────┬──────┴──────┬──────┘
       │             │             │
       └─────────────┼─────────────┘
                     │
              ┌──────▼──────┐
              │  SYNTHESIZE │
              └─────────────┘
```

### Sequential (Exploration Path)

Use when question needs clarification:

```
┌──────────┐
│ deepwiki │ → Understand the landscape
└────┬─────┘
     │
┌────▼─────┐
│ Submodule│ → Verify with examples
└────┬─────┘
     │
┌────▼─────┐
│ Codebase │ → Check TMNL context
└────┬─────┘
     │
┌────▼──────┐
│ SYNTHESIZE│
└───────────┘
```

---

## Failure Modes

### Mode 1: deepwiki Returns Vague Response

**Symptom**: "It depends on your use case..."

**Action**: Escalate immediately to submodules with specific code search

### Mode 2: Submodules May Be Outdated

**Symptom**: Submodule git log shows old commit

**Action**: Check with WebSearch for recent changes, then verify

### Mode 3: Conflicting Information

**Symptom**: deepwiki and submodule say different things

**Action**:
1. Check dates (which is newer?)
2. Prefer test patterns over prose
3. Verify with WebSearch if needed
4. Admit uncertainty if unresolved

### Mode 4: No Information Found

**Symptom**: All sources return empty/irrelevant

**Action**:
1. Rephrase query with different terms
2. Check if this is a TMNL-specific pattern
3. Admit uncertainty explicitly
4. Suggest experimental approach

---

## Quick Reference

### Decision Tree

```
Is this an Effect API question?
├─ YES → deepwiki first, then submodules
└─ NO
   ├─ Is this TMNL-specific?
   │  └─ YES → Codebase first
   └─ Is this a recent change question?
      └─ YES → WebSearch first, verify with submodules
```

### Minimum Viable Research

For quick questions, at minimum:

1. **One deepwiki query** (verification style)
2. **One submodule check** (website or test)
3. **State confidence level**

### Full Research Protocol

For important patterns:

1. deepwiki structure query
2. deepwiki verification query
3. Website submodule check
4. Effect test pattern check
5. Codebase precedent check
6. Synthesis with confidence level

---

## Integration Points

| Skill | Role in Cascade |
|-------|-----------------|
| `/grounded-research` | Uncertainty protocol |
| `/effect-research` | Effect-specific queries |
| `/tmnl-submodule-exploration` | Submodule navigation |
| Domain skills (`/effect-patterns`, etc.) | Implementation after research |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/creatifcoding) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
