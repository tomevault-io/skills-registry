---
name: fullcheck
description: Comprehensive writing review — spawns analyser agents for thorough quality and voice assessment. Use when the user asks for a "full check", "thorough review", "comprehensive edit", or wants detailed quality analysis of their writing. Use when this capability is needed.
metadata:
  author: dannysmith
---

# /fullcheck — Comprehensive Writing Review

Thorough quality review using `writing-analyser` subagents. Use this for important documents that need a proper edit.

## Determine Input

Figure out what to check:
- **File path(s) passed as arguments** → use those files
- **Directory path** → use the files in it (markdown/text files)
- **Inline text** → use it directly
- **Nothing passed** → look at recent conversation context for text being discussed. If genuinely unclear, ask the user what they'd like checked.

## Spawn Analyser Agents

Launch `writing-analyser` subagents in parallel using the Task tool. Split the work sensibly:

**For a single document:**
- **Agent 1**: Focus on nonos.md compliance and general writing quality (slop, clarity, word choice, rhythm)
- **Agent 2**: Focus on Danny's voice, structure, and overall effectiveness (voice compliance, structural patterns, document flow)

**For multiple documents:**
- One agent per document (up to 3-4), each doing a full review

Give each agent clear instructions about what to focus on and pass the file path(s) or text.

## Compile Results

Once the agents return, compile their reports into a single comprehensive review:

1. **Deduplicate** — if both agents flag the same issue, list it once
2. **Prioritise** — order by severity (critical → warnings → suggestions)
3. **Summarise** — add a brief overall assessment at the top

## Output Format

```markdown
## Overall Assessment
[2-3 sentences: what's the text doing well, what needs the most work]

## Critical Issues
[Must fix — slop, errors, broken clarity]

## Warnings
[Should fix — weak language, structural problems, voice misses]

## Suggestions
[Nice to have — polish, enhancement opportunities]

## CLI Tool Output
[If agents ran write-good/proselint, include notable findings here]
```

Keep the compiled report concise and actionable. The user wants to know what to fix, not read an essay about their essay.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dannysmith) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
