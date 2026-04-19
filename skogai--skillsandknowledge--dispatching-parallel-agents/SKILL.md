---
name: dispatching-parallel-agents
description: Use when facing 3+ independent tasks that can be completed without shared state or dependencies - dispatches multiple agents to work concurrently on summarization, investigation, implementation, or analysis
metadata:
  author: skogai
---

# Dispatching Parallel Agents

## Overview

When you have multiple independent tasks, doing them sequentially wastes time. Each task is independent and can happen in parallel.

**Core principle:** Dispatch one agent per independent task. Let them work concurrently.

## When to Use

**Use when:**
- 3+ tasks that are completely independent
- No shared state between tasks
- Each task can be understood without context from others
- Tasks don't need to coordinate or share results until completion

**Common scenarios:**
- Summarizing multiple independent documents/skills
- Investigating multiple unrelated failures (different test files, different subsystems)
- Analyzing multiple independent codebases or components
- Implementing multiple independent features in separate modules

**Don't use when:**
- Tasks are related (output of one affects another)
- Need to understand full system state across all tasks
- Agents would interfere with each other (editing same files, shared resources)
- Sequential work is required (one task depends on previous)

## The Pattern

### 1. Identify Independent Tasks

Group work by what can be done in parallel:
- Document A: Summarize content and list files
- Document B: Summarize content and list files
- Document C: Summarize content and list files

Each task is independent - doing A doesn't affect B or C.

### 2. Create Focused Agent Tasks

Each agent gets:
- **Specific scope:** One clear deliverable
- **Clear goal:** What to produce
- **Constraints:** Don't exceed scope
- **Expected output:** Exactly what you need back

### 3. Dispatch in Parallel

```
Task("Summarize skills batch 1: ansible-core, arch-wiki, ...")
Task("Summarize skills batch 2: brainstorming, condition-based-waiting, ...")
Task("Summarize skills batch 3: problem-solving, receiving-code-review, ...")
// All run concurrently
```

### 4. Collect and Integrate

When agents return:
- Read each result
- Verify quality
- Combine into final output
- Check for conflicts (if applicable)

## Agent Prompt Structure

Good agent prompts are:
1. **Focused** - One clear deliverable
2. **Self-contained** - All context needed
3. **Specific about output** - Exact format expected

**Example (summarization):**
```markdown
Analyze these skill directories in /path/to/skills:
- skill-a
- skill-b
- skill-c

For each skill:
1. Read the SKILL.md file and provide a 2-3 sentence summary
2. List any other files with a one-sentence description of each

Format your response as:
## skill-name
**Summary:** [2-3 sentences]
**Additional files:**
- filename: description

Return the complete analysis.
```

**Example (investigation):**
```markdown
Fix the 3 failing tests in src/agents/agent-tool-abort.test.ts:

1. "should abort tool with partial output" - expects 'interrupted at' in message
2. "should handle mixed completed and aborted" - fast tool aborted instead of completed
3. "should properly track pendingToolCount" - expects 3 results but gets 0

Your task:
1. Read the test file and understand what each test verifies
2. Identify root cause
3. Fix the issues
4. Verify tests pass

Return: Summary of what you found and what you fixed.
```

## Common Mistakes

**❌ Too broad:** "Summarize all the skills" - too much for one agent
**✅ Specific:** "Summarize skills batch 1: ansible-core, arch-wiki, brainstorming, condition-based-waiting, defense-in-depth"

**❌ No context:** "Fix the race condition" - agent doesn't know where
**✅ Context:** Include file paths, error messages, test names

**❌ No constraints:** Agent might go beyond scope
**✅ Constraints:** "Only analyze these 5 files, don't investigate dependencies"

**❌ Vague output:** "Do the work" - you don't know what you'll get
**✅ Specific:** "Return summary in this exact format: [format example]"

## When NOT to Use

**Related tasks:** Doing one affects others - do together or sequentially
**Need full context:** Understanding requires seeing entire system
**Exploratory work:** You don't know what needs to be done yet
**Shared state:** Agents would interfere (editing same files, using same database)

## Real Examples

**Scenario 1: Summarizing 25 skills**
- **Decision:** Split into 5 batches of 5 skills each
- **Dispatch:** 5 agents in parallel, each summarizes their batch
- **Result:** Complete in 1/5th the time vs sequential

**Scenario 2: 6 test failures across 3 files**
- **Failures:**
  - agent-tool-abort.test.ts: 3 failures (timing issues)
  - batch-completion-behavior.test.ts: 2 failures (tools not executing)
  - tool-approval-race-conditions.test.ts: 1 failure (execution count = 0)
- **Decision:** Independent domains - abort logic separate from batch completion
- **Dispatch:** 3 agents in parallel, one per test file
- **Results:** All fixed independently, no conflicts

**Scenario 3: Analyzing 4 microservices**
- **Services:** auth-service, payment-service, notification-service, analytics-service
- **Decision:** Each service independent, different codebases
- **Dispatch:** 4 agents in parallel, each analyzes one service
- **Result:** Complete architectural overview in parallel

## Key Benefits

1. **Parallelization** - Multiple tasks happen simultaneously
2. **Focus** - Each agent has narrow scope, less context to track
3. **Independence** - Agents don't interfere with each other
4. **Speed** - N problems solved in time of 1

## Verification

After agents return:
1. **Review each result** - Understand what was delivered
2. **Check for conflicts** - Did agents contradict each other? (if applicable)
3. **Verify quality** - Did each agent complete their task correctly?
4. **Spot check** - Agents can make systematic errors

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/skogai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
