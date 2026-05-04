---
name: brainstorming
description: Brainstorm and advise on technical decisions using structured process and MCP helpers. EXCLUSIVE to brainstormer agent. Does NOT implement — only advises. Use when this capability is needed.
metadata:
  author: neversight
---
# Brainstorming

**Exclusive to:** `brainstormer` agent

> ⚠️ **CRITICAL**: This skill is for brainstorming and advising ONLY. Do NOT implement solutions.

## MCP Helpers (Brain + Memory)

### 🧠 Gemini-Bridge (Brain)
Use for deep reasoning, architecture analysis, and creative problem-solving:
```
mcp_gemini-bridge_consult_gemini(query="Analyze trade-offs for [topic]...", directory=".")
```

### 🌉 Open-Bridge
Alternative deep reasoning:
```
mcp_open-bridge_consult_gemini(query="Analyze trade-offs for [topic]...", directory=".")
```

### 📚 Context7 (Memory)
Use for up-to-date library docs and best practices:
```
mcp_context7_resolve-library-id(libraryName="[lib]", query="[topic]")
mcp_context7_query-docs(libraryId="/[id]", query="[specific question]")
```

## Instructions

1. **Discover** — Ask clarifying questions about requirements, constraints, timeline
2. **Research** — Gather information from codebase and external sources
3. **Analyze** — Evaluate multiple approaches with pros/cons
4. **Debate** — Present options, challenge assumptions, find optimal solution
5. **Consensus** — Ensure alignment on chosen approach
6. **Document** — Create comprehensive summary report

## Output Template

```markdown
# Brainstorm Summary: [Topic]

## Problem Statement
[Description]

### Requirements
- [Requirement]

### Constraints
- [Constraint]

## Evaluated Approaches

### Option A: [Name]
| Pros | Cons |
|------|------|
| [Pro] | [Con] |

### Option B: [Name]
[Same structure]

## Recommended Solution
[Decision and rationale]

## Risks & Mitigations
| Risk | Impact | Mitigation |
|------|--------|------------|

## Success Metrics
- [ ] [Metric]

## Next Steps
1. [Step] — [Owner]
```

## Decision Frameworks

### Weighted Criteria
| Criteria | Weight |
|----------|--------|
| Feasibility | 30% |
| Maintainability | 25% |
| Performance | 20% |
| Time to build | 25% |

### SCAMPER
- **S**ubstitute — What can be replaced?
- **C**ombine — What can be merged?
- **A**dapt — What can we borrow?
- **M**odify — What can change?
- **P**ut to other uses — New applications?
- **E**liminate — What can be removed?
- **R**everse — What if opposite?

## Examples
- "Brainstorm architecture for feature X"
- "Compare these two technical approaches"
- "Help me decide between options"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
