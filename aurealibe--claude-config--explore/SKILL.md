---
name: explore
description: Deep codebase exploration to answer specific questions Use when this capability is needed.
metadata:
  author: aurealibe
---

**YOU ARE EXECUTING THE `/explore` SKILL.** The user triggered this skill. Follow ALL instructions below step by step. Do NOT treat this as a freeform conversation - execute the skill workflow.

Read-only exploration task.

## Ultra Think Strategy

Ultra think before answering:
- After exploration results: reflect on completeness, identify gaps
- Before answering: consider multiple interpretations, ensure accuracy
- Cross-reference findings from different sources

---

## 1. PARSE QUESTION

Extract from `$ARGUMENTS`:
- Key terms and concepts to search
- Whether backend, frontend, or both
- Whether external documentation is needed

---

## 1.5 CLARIFY IF AMBIGUOUS

Use AskUserQuestion if the question could have multiple interpretations:
- "How does X work?" -> Which layer? What aspect?
- "Where is X handled?" -> At which level?
- "Find X" -> Find what exactly? (usage/definition/all)

If question is clear and specific, skip to section 2.

---

## 2. EXPLORE (PARALLEL)

Launch focused agents in a **single message** (parallel execution).

For backend/frontend questions, launch **at least 2 agents per domain** to avoid shallow results:

**Backend** (pick 2-3 relevant):
1. `explore-codebase`: "Find [keywords] definitions and core logic in backend/internal/domain/ and backend/internal/application/"
2. `explore-codebase`: "Find [keywords] implementations and usage in backend/internal/infrastructure/ and backend/internal/presentation/"
3. `explore-codebase`: "Find similar patterns to [keywords] in backend/ for comparison"

**Frontend** (pick 2-3 relevant):
1. `explore-codebase`: "Find [keywords] components and hooks in frontend/src/components/ and frontend/src/hooks/"
2. `explore-codebase`: "Find [keywords] types, API calls, and pages in frontend/src/types/, frontend/src/lib/, and frontend/src/app/"
3. `explore-codebase`: "Find similar patterns to [keywords] in frontend/src/ for comparison"

**Supporting** (as needed):
- Database: `explore-db` - "[env] - [tables/schema question]"
- Library: `explore-docs` - "[library] [feature]"
- External: `websearch` - "[topic]"

For simple single-file lookups, 1 agent per domain is sufficient.

---

## 2.5 POST-EXPLORATION CHECK

After agents return, verify coverage:

1. **Full code path traced?** Can I trace the complete flow? If gaps -> launch targeted `explore-codebase`
2. **Similar patterns identified?** Do I have a reference implementation? If not -> launch `explore-codebase`
3. **Data model complete?** If not -> launch `explore-db`
4. **Library docs sufficient?** If not -> launch `explore-docs`

---

## 3. ANSWER

Provide comprehensive response:

```markdown
## Answer

[Direct answer to the question]

## Evidence

### Code Found
- `path/to/file.ts:XX` - [description]
- `path/to/file.go:YY` - [description]

### Patterns Identified
- [Pattern 1]: [explanation]

### Documentation (if explore-docs used)
- [Library]: [relevant info]

### External Sources (if websearch used)
- [Title](URL) - [what we learned]

## Recommendations (if applicable)
- [Suggestion based on findings]
```

---

## Rules

- **PARALLEL** - launch relevant agents in ONE message
- **CITE** - always reference file:line
- **READ-ONLY** - do not modify anything
- **THOROUGH** - gather complete context before answering

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aurealibe) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
