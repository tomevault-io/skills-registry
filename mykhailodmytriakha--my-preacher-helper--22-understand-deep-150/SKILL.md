---
name: 22-understand-deep-150
description: [22] UNDERSTAND. Deep research from all sources — internal (code, git, logs) AND external (web, docs, best practices). Use when choosing libraries, investigating solutions, understanding legal/technical questions, comparing approaches, or anytime you need comprehensive knowledge from both project context and world knowledge. Triggers on \"research\", \"investigate\", \"find best approach\", \"what do others do\", \"compare options\", or complex questions requiring multiple sources. Use when this capability is needed.
metadata:
  author: mykhailodmytriakha
---

# Understand-Deep 150 Protocol

**Core Principle:** Research deeply from ALL sources — internal context AND external knowledge. Don't limit yourself to one world.

## What This Skill Does

When you invoke this skill, you're researching from TWO worlds:

**🏠 INTERNAL (Project Context)**
- Code — what's already implemented
- Git history — why and when things changed
- Logs — what actually happened
- Config — how things are set up

**🌍 EXTERNAL (World Knowledge)**
- Web search — how others solved similar problems
- Documentation — official guidance and specs
- Best practices — established patterns
- Community — Stack Overflow, GitHub issues, forums

## The Two Worlds of Research

```
🏠 INTERNAL (Project Context)        🌍 EXTERNAL (World Knowledge)
├── Code files                       ├── Web search
├── Git history/blame                ├── Official documentation
├── Comments & inline docs           ├── Stack Overflow / forums
├── Config files                     ├── Blog posts & tutorials
├── Tests (expected behavior)        ├── GitHub issues/discussions
├── Logs (actual behavior)           ├── Best practice guides
└── Project documentation            └── Comparison articles
```

**Key Insight:** Most questions need BOTH worlds. Internal tells you "what we have", external tells you "what's possible".

## The 150% Research Rule

- **100% Core:** Research from primary relevant sources (internal OR external depending on question)
- **50% Enhancement:** Cross-check with the OTHER world (if started internal → check external, vice versa)

## Real-World Research Scenarios

| Scenario | Internal Research | External Research |
|----------|-------------------|-------------------|
| **Choosing a library** | What's already used? Conflicts? | Comparisons, benchmarks, community health |
| **Legal question** | Context, contracts, history | Laws, regulations, precedents |
| **Architecture decision** | Current structure, constraints | Patterns, case studies, trade-offs |
| **Bug investigation** | Code, logs, git blame | Known issues, similar problems |
| **Performance optimization** | Profiling, bottlenecks | Best practices, techniques |
| **Security concern** | Current implementation | Vulnerabilities, OWASP, CVEs |

## When to Use Which Source

### 🔍 **Understanding Existing Code**
| Question | Best Source | Tools* |
|----------|-------------|--------|
| "What does this code do?" | Read the code | `read_file`, `grep` |
| "Why was it written this way?" | Git blame/history | `git log`, `git blame` |
| "When did this change?" | Git history | `git log --follow` |
| "What was the original intent?" | Commits + PR descriptions | `git show`, `git log` |

### 🌐 **Finding Solutions**
| Question | Best Source | Tools* |
|----------|-------------|--------|
| "How do others solve X?" | Web search | `web_search` |
| "What's the best practice?" | Web + official docs | `web_search` |
| "Is there a library for X?" | Web + package repos | `web_search` |
| "What are common pitfalls?" | Web + Stack Overflow | `web_search` |

### 📖 **Understanding Context**
| Question | Best Source | Tools* |
|----------|-------------|--------|
| "What does this API do?" | Official documentation | `web_search` for docs |
| "What version is compatible?" | Package docs + changelogs | `web_search`, `read_file` |
| "How has this evolved?" | Git history | `git log`, `git diff` |

*Tool names vary by agent — use equivalent available tools

## Tool Abstraction Guide

Different agents have different tool names. Use what's available:

| Capability | Claude Code | Codex | Generic |
|------------|-------------|-------|---------|
| Read files | `read_file` | `view_file` | file reading tool |
| Search code | `grep`, `codebase_search` | `search` | code search tool |
| Web search | `web_search` | `search_web` | internet search tool |
| Run commands | `run_terminal_cmd` | `shell` | terminal/shell tool |
| Git operations | terminal + git | terminal + git | git via terminal |

**Rule:** Use the tool that achieves the goal, regardless of its name.

## Execution Protocol

### Step 1: IDENTIFY WHAT YOU NEED
Clarify the knowledge gap:
- What exactly do I need to know?
- Is this about existing code or new solutions?
- Do I need history/context or just current state?

### Step 2: SELECT SOURCES
Choose appropriate sources based on need:

```
Need to understand existing code?
  → Read code + git history + comments

Need to find a solution approach?
  → Web search + best practices + similar projects

Need to debug something?
  → Logs + git blame + error searches

Need to integrate with external system?
  → Official docs + web search + examples
```

### Step 3: GATHER FROM MULTIPLE SOURCES
Minimum 3 sources for important facts:
- Primary source (code, official docs)
- Secondary source (explanations, tutorials)
- Validation source (tests, examples, community)

### Step 4: CROSS-VALIDATE
Check for consistency:
- Do sources agree?
- Are there contradictions?
- Which source is most authoritative?

### Step 5: SYNTHESIZE
Combine findings into verified knowledge:
- Core facts (verified)
- Context (understood)
- Confidence level (quantified)

## Git History: Your Hidden Knowledge Source

Git history is often overlooked but invaluable:

### When to Use Git
| Situation | Git Command | What You Learn |
|-----------|-------------|----------------|
| "Why is this code weird?" | `git blame <file>` | Who wrote it and when |
| "What was the intent?" | `git log -p <file>` | Changes over time |
| "When did this break?" | `git bisect` | Which commit caused issue |
| "What changed recently?" | `git log --since="1 week"` | Recent modifications |
| "How did this function evolve?" | `git log -p -S "function_name"` | History of specific code |

### Git Evidence Patterns
```bash
# Who wrote this line and why?
git blame path/to/file.js

# What changed in this file and why?
git log -p --follow path/to/file.js

# Find when a specific line was added
git log -S "specific code" --oneline

# What happened around a date?
git log --since="2024-01-01" --until="2024-02-01"
```

## Web Search: Your External Brain

Web search is essential for:
- Best practices and patterns
- How others solved similar problems
- Library/API documentation
- Error message solutions
- Community knowledge

### Effective Web Search Patterns
```
"How to [specific task] in [language/framework]"
"[error message] solution"
"[library name] best practices"
"[problem] vs [alternative] comparison"
"[technology] production tips"
```

## Output Format

When using Research-Deep 150:

```
🔬 **Research-Deep 150 Complete**

**Question:** [What we needed to know]

**🏠 Internal Research:**
- 📁 Code: [what we found in codebase]
- 📜 Git: [what history revealed]
- 📋 Context: [relevant project facts]

**🌍 External Research:**
- 🌐 Web: [what we found online]
- 📖 Docs: [official documentation]
- 💡 Best Practices: [established patterns]

**Synthesis:**
[Combined understanding from BOTH worlds]

**Recommendation:** [What to do based on research]

**Confidence:** [X]%
**Sources agree:** [Yes/Partial/No]
**Gaps:** [What we still don't know]
```

## Evidence Quality Levels

| Level | Sources | Confidence |
|-------|---------|------------|
| **Strong** | 3+ agreeing sources, primary data | 90-100% |
| **Good** | 2-3 sources, mostly agree | 75-89% |
| **Moderate** | 1-2 sources, some assumptions | 50-74% |
| **Weak** | Single source or contradictions | <50% |

## Operational Rules

1. **NEVER SINGLE SOURCE:** Important facts need 3+ sources
2. **USE ALL AVAILABLE:** Code, git, web, docs — use what's relevant
3. **CONTEXT MATTERS:** Choose sources based on what you need to know
4. **GIT IS GOLD:** History often explains the "why"
5. **WEB FOR PATTERNS:** Others have solved your problem before
6. **CROSS-VALIDATE:** Sources should agree or explain why not
7. **QUANTIFY CONFIDENCE:** State how sure you are and why

## Examples

### ❌ Without Research-Deep
```
Question: "Which date library should we use?"
AI: "Use moment.js, it's popular" (outdated advice)
Result: Chose deprecated library, had to migrate later
```

### ✅ With Research-Deep 150
```
Question: "Which date library should we use?"

🔬 Research-Deep 150:

🏠 Internal Research:
- 📁 Code: Currently using native Date, some formatting issues
- 📋 Context: Need timezone support, bundle size matters
- 📜 Git: Previous attempt with moment.js reverted (too heavy)

🌍 External Research:
- 🌐 Web: "best javascript date library 2024"
- 📖 Docs: Compared date-fns, dayjs, luxon
- 💡 Best Practices: moment.js deprecated, modern alternatives exist

Synthesis:
- moment.js is deprecated (external confirms)
- We tried it before, too heavy (internal confirms)
- date-fns: modular, tree-shakeable, good for bundle size
- dayjs: moment-like API, small size
- luxon: best for complex timezone work

Recommendation: dayjs for simple needs (2KB), date-fns for complex (modular)

Confidence: 92%
```

### ✅ Legal Question Example
```
Question: "Can we use this image in our product?"

🔬 Research-Deep 150:

🏠 Internal Research:
- 📁 Found image in /assets/hero.jpg
- 📜 Git: Added 2 years ago, no license info in commit
- 📋 Context: Used on marketing page

🌍 External Research:
- 🌐 Reverse image search: Found on Unsplash
- 📖 Unsplash license: Free for commercial use, no attribution required
- 💡 Best Practice: Still good to keep license record

Recommendation: Safe to use. Document source in assets README.

Confidence: 95%
```

## Failure Modes & Recovery

| Failure | Detection | Recovery |
|---------|-----------|----------|
| **Internal only** | No external perspective | Add web search, docs |
| **External only** | Ignoring project context | Check code, git, configs |
| **Guessing** | No sources cited | Stop, research properly |
| **Outdated info** | Old articles, deprecated advice | Check dates, find current sources |
| **One world bias** | Only looking at one side | Deliberately check the other world |

## Relationship to Other Skills

| Skill | Focus |
|-------|-------|
| **goal-clarity-150** | WHAT we need to know |
| **research-deep-150** | WHERE to find answers (internal + external) |
| **deep-think-150** | HOW to reason about findings |
| **impact-map-150** | WHAT the findings affect |

## Session Log Entry (MANDATORY)

After completing this skill, write to `.sessions/SESSION_[date]-[name].md`:

```
### [date - HH"MM] Understand-Deep 150 Complete
**Topic:** <research topic>
**Sources:** <internal/external sources used>
**Key Findings:** <bullet points>
**Synthesis:** <combined insight>
```

---

**Remember:** Most questions need BOTH worlds. Internal context tells you "what we have and why". External knowledge tells you "what's possible and what others learned". Deep research combines both for complete understanding.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mykhailodmytriakha) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
