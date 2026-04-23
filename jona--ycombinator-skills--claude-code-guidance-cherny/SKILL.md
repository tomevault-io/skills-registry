---
name: claude-code-best-practices
description: Best practices for using Claude Code effectively based on insights from its creator Boris Cherny. Trigger this skill when users ask about optimizing Claude Code usage, configuring CLAUDE.md files, using plan mode, working with sub-agents, understanding Claude Code philosophy, improving coding productivity with Claude Code, or building AI coding tools. Also trigger when users mention blatant demand, scaffolding in AI products, building for future model capabilities, or ask about Anthropic's approach to AI coding assistants. Use when this capability is needed.
metadata:
  author: jona
---

# Claude Code Best Practices

Guidance for using Claude Code effectively and understanding its design philosophy, based on insights from Boris Cherny, the creator of Claude Code.

## Core Philosophy

### Build for T+6 Months

Design and configure for the model six months from now, not today's limitations:

- Avoid over-engineering workarounds for current model weaknesses
- Expect model improvements to obsolete most scaffolding code
- Treat compensatory instructions in CLAUDE.md as temporary

### Blatant Demand Principle

Only optimize behaviors users already perform:

- Observe what you do manually and repeatedly
- Make existing workflows easier, don't invent new ones
- If you're not already doing something manually, Claude Code won't magically make you do it

### Scaffolding is Tech Debt

All code and configuration compensating for model limitations is temporary:

- Expect to delete or rewrite within 6 months
- Don't over-invest in workarounds
- Model improvements typically provide 10-20% gains that obsolete previous scaffolding

## CLAUDE.md Configuration

### Keep It Minimal

```markdown
# Project: [Name]

## Context
[One paragraph describing what this project does]

## Key Patterns
- [Pattern 1]: [Brief explanation]
- [Pattern 2]: [Brief explanation]

## Preventable Mistakes
- [Mistake to avoid]
- [Another mistake to avoid]

## Commands
- Build: `[command]`
- Test: `[command]`
- Deploy: `[command]`
```

### When to Delete and Start Fresh

Delete CLAUDE.md and restart when:

- File exceeds ~500 lines
- Instructions contradict each other
- Many instructions address obsolete model limitations
- You notice Claude ignoring parts of the file

### What to Add Immediately

Add to CLAUDE.md when you notice:

- Repeated mistakes in PR reviews
- Project-specific conventions Claude keeps missing
- Build or test commands that require explanation

### What to Avoid

Do not add:

- General coding best practices (Claude already knows these)
- Verbose explanations of obvious patterns
- Instructions for problems that newer models handle automatically

## Plan Mode

### What It Is

Plan mode is a single prompt instruction that prevents Claude from writing code immediately:

```
Please think through this problem step by step without writing code yet.
```

### When to Use Plan Mode

Use for approximately 80% of sessions, especially:

- Complex tasks with multiple components
- Tasks requiring architectural decisions
- Debugging sessions where root cause is unclear
- Unfamiliar codebases or domains

### When to Skip Plan Mode

Skip for:

- Trivial changes (typo fixes, simple renames)
- Well-defined tasks with clear implementation
- Follow-up work where plan already exists

### Implementing Automatic Plan Mode

Add to CLAUDE.md for complex projects:

```markdown
## Default Behavior
For any task involving more than a single file change, start by outlining the approach before writing code.
```

## Sub-Agent Workflows

### Calibrating Agent Count

Match parallelism to task difficulty:

| Task Type | Agents | Example |
|-----------|--------|---------|
| Simple lookup | 1 | "What does this function do?" |
| Moderate research | 3-5 | "Find all usages of this API" |
| Complex debugging | 5-10 | "Why is this test flaky?" |
| Broad codebase analysis | 10+ | "Audit all authentication flows" |

### Uncorrelated Context Windows

Sub-agents provide value through fresh, independent context:

- Each agent starts without pollution from other agents' reasoning
- Enables parallel exploration of different hypotheses
- Reduces anchoring bias from early incorrect conclusions

### Prompting Sub-Agents

The parent Claude instance (Mama Claude) spawns sub-agents with specific, focused prompts:

```
Investigate whether the database connection pool is exhausted during peak load.
Focus only on connection lifecycle and pool configuration.
Report findings without implementing fixes.
```

## Productivity Patterns

### Observed Gains

Anthropic engineers report 150% productivity improvement - unprecedented compared to typical developer tooling improvements of 2%.

### Maximizing Effectiveness

1. **Start sessions with context**: Brief project description before diving into tasks
2. **Iterate rapidly**: Prototype 20 variations quickly instead of 3 polished ones
3. **Let Claude explore**: Remove barriers rather than constraining to predefined APIs
4. **Review output critically**: Productivity gains come from faster iteration, not blind acceptance

### Common Anti-Patterns

Avoid these behaviors that reduce effectiveness:

- Over-specifying implementation details in prompts
- Interrupting Claude mid-task with corrections
- Accumulating stale instructions in CLAUDE.md
- Fighting the model instead of working with its tendencies

## Building AI Coding Tools

### Product Overhang

Model capabilities often exist before products harness them:

- Observe what the model tries to do naturally
- Remove barriers to those behaviors
- Build interfaces that expose existing capabilities

### The Bitter Lesson Applied

More general approaches beat specialized ones:

- Don't over-engineer for specific edge cases
- Trust model improvements to handle current weaknesses
- Avoid betting against improving model capabilities

### Rapid Prototyping

When building features:

1. Build minimal version in hours, not days
2. Get to users immediately
3. Observe actual usage patterns
4. Iterate based on real feedback

Example: Plan mode shipped in 30 minutes after observing user patterns.

## Workflow: Starting a New Coding Session

1. **Assess task complexity**
   - Trivial: Skip to step 4
   - Moderate to complex: Continue to step 2

2. **Enter plan mode**
   - Describe the task clearly
   - Ask Claude to outline approach before coding
   - Review and refine the plan

3. **Decide on parallelism**
   - Single-focus task: Proceed with one context
   - Research/debugging: Spawn appropriate sub-agents

4. **Execute with iteration**
   - Let Claude work through the plan
   - Provide feedback on output
   - Iterate until complete

5. **Capture learnings**
   - Add preventable mistakes to CLAUDE.md
   - Remove obsolete instructions
   - Note patterns for future sessions

## Workflow: Maintaining CLAUDE.md

1. **Weekly review**
   - Scan for instructions no longer needed
   - Check for contradictions
   - Verify commands are current

2. **Post-PR review**
   - Add any repeated mistakes immediately
   - Update patterns that Claude missed

3. **Monthly reset evaluation**
   - If file exceeds 500 lines, consider fresh start
   - Archive old version for reference
   - Rebuild with only essential instructions

## Key Predictions and Context

Based on Claude Code's development trajectory:

- Terminal interface continues to work despite being "accidental"
- GUI versions (Claude Code Work) extend capability to non-technical users
- Expect significant capability improvements every 3-6 months
- Most valuable engineering skills shifting to product sense and user research

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jona) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
