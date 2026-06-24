---
name: antibrittle-patterns
description: This skill should be used when the user asks about "long-running agents", "antibrittle patterns", "agent reliability", "agents that run for hours", "run boxes", "context boundaries", or needs architecture guidance for AI agents that work autonomously for extended periods. Use when this capability is needed.
metadata:
  author: bigadamknight
---

# Antibrittle Agent Patterns

Patterns and principles for building AI agents that can run for hours or days without breaking. Not just robust (survives stress) but antibrittle (improves under stress).

## Core Philosophy

Traditional agent design treats failure as the enemy. Antibrittle design treats failure as information. The goal isn't to prevent all failures - it's to build systems that:

1. Detect problems early
2. Recover gracefully
3. Learn from failures
4. Get more reliable over time

## The Five Principles

### 1. Run Boxes (Behavioral Monitoring)

Don't evaluate single LLM calls. Monitor agent behavior at the loop level.

**The Rice Cooker Analogy**: A rice cooker doesn't check "is this grain cooked?" - it monitors temperature change rate. When the rate changes, rice is done. Agents need similar system-level signals.

**Define your run box**:
- What signals indicate healthy operation?
- What patterns suggest failure modes?
- What's the expected behavioral range?

**Example Run Box for a Coding Agent**:
```
Healthy signals:
- File reads followed by edits
- Test runs after changes
- Commits after passing tests

Warning signals:
- 3+ consecutive failed test runs
- Reading same file 5+ times
- No commits in 30+ minutes of activity

Failure signals:
- Editing files outside project scope
- Deleting files without backup
- Infinite loops in tool calls
```

### 2. Regions of Freedom

Don't force linear TODO lists. Allow exploration AND exploitation.

**Bad**: "Do step 1, then step 2, then step 3"
**Good**: "Here are the interconnected subproblems. Solve them in whatever order makes sense."

**Why it works**:
- Real problem-solving is non-linear
- Agents discover dependencies through exploration
- Forcing linearity causes thrashing when stuck

**Implementation**:
```markdown
## Task: Implement User Authentication

Subproblems (solve in any order):
- [ ] Database schema for users
- [ ] Password hashing utility
- [ ] Session management
- [ ] Login API endpoint
- [ ] Registration API endpoint
- [ ] Email verification flow

Dependencies will emerge. That's fine.
```

### 3. Trenches (Context Boundaries)

Create hard boundaries between operational domains. Prevent "worldline rot" - the gradual degradation of prompts and context as systems accumulate complexity.

**Signs of worldline rot**:
- Agent behavior drifts over time
- Prompt becomes cluttered with exceptions
- Tool definitions grow unbounded
- Context window fills with irrelevant history

**Implementing trenches**:

1. **Frequent state resets**: Start fresh for each major task
2. **Clear handoff points**: Explicit "end of phase" markers
3. **Domain isolation**: Don't mix concerns in same context
4. **Checkpoint and resume**: Save state, start new context, restore essential state

**Example Trench Pattern**:
```
[Research Phase]
  - Can read files, search web
  - Cannot write files
  - Outputs: research_summary.md

---TRENCH: Research complete, starting implementation---

[Implementation Phase]
  - Reads research_summary.md
  - Can read/write project files
  - Cannot search web
  - Outputs: code changes

---TRENCH: Implementation complete, starting verification---

[Verification Phase]
  - Can read files, run tests
  - Cannot write code
  - Outputs: test_report.md
```

### 4. Receipts Over Perfection

Aim for "five nines of accountability" rather than five nines of reliability.

**The insight**: Perfect reliability is impossible. Perfect traceability is achievable.

**Every output should connect to**:
- Source materials used
- Decisions made and why
- Alternative approaches considered
- Confidence levels
- Known limitations

**Implementation**:
```markdown
## Decision: Using PostgreSQL over MongoDB

Sources consulted:
- Project requirements doc (line 45-60)
- Team tech radar (databases section)
- Performance benchmarks from https://...

Reasoning:
- Relational data model fits user-post-comment structure
- Team has PostgreSQL expertise
- ACID compliance required for payments

Alternatives considered:
- MongoDB: Better for unstructured data, but our data is structured
- SQLite: Too limited for production scale

Confidence: High (8/10)
Limitations: May need to revisit if we add real-time features
```

### 5. Minimal Tooling

A focused toolkit outperforms sprawling MCPs.

**The temptation**: Add every tool that might be useful.
**The reality**: More tools = more confusion = more errors.

**Core toolkit for most agents**:
- `read` - Read files
- `edit` - Edit files
- `shell` - Run commands
- `search` - Find information

**Add tools only when**:
- Core toolkit genuinely can't do the job
- You've hit the limitation 3+ times
- The tool is well-tested

### 6. Avoid Over-Parallelization

Single-threaded often beats multi-agent.

**Why parallel agents fail**:
- Communication overhead exceeds time saved
- Merge conflicts in shared state
- Harder to debug and trace
- Coordination logic is complex

**When parallel works**:
- Truly independent tasks
- No shared state
- Clear merge strategy
- Well-defined interfaces

**Default to sequential. Parallelize only when proven beneficial.**

## Decision Framework: When to Intervene

```
Is agent making progress?
├─ Yes → Let it continue
└─ No → Has it been stuck for > N minutes?
    ├─ No → Let it continue (exploring)
    └─ Yes → Is it trying different approaches?
        ├─ Yes → Increase N, let it continue
        └─ No → Intervene with guidance
```

## Anti-Patterns to Avoid

### 1. The God Prompt
Trying to anticipate every scenario in one massive prompt.
**Fix**: Use trenches and focused phases.

### 2. The Tool Hoarder
Adding MCPs "just in case."
**Fix**: Start minimal, add only when proven necessary.

### 3. The Micromanager
Checking after every single step.
**Fix**: Define run boxes, intervene only on warning signals.

### 4. The Perfectionist
Refusing to ship until 100% reliable.
**Fix**: Focus on accountability, ship with transparency.

### 5. The Parallelist
Spawning agents for everything.
**Fix**: Default sequential, prove parallel benefit first.

## Practical Checklist

Before running a long-horizon agent:

- [ ] Run box defined (healthy/warning/failure signals)
- [ ] Trenches planned (phase boundaries)
- [ ] Checkpointing strategy (how to resume if interrupted)
- [ ] Accountability format (how outputs link to sources)
- [ ] Intervention threshold (when to step in)
- [ ] Toolkit reviewed (minimal tools needed)

## Credits

Based on [Southbridge AI's Antibrittle Agents](https://www.southbridge.ai/blog/antibrittle-agents) research, referenced by [@hrishioa](https://x.com/hrishioa/status/2006061257741471979).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bigadamknight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
