---
name: workflow-creator
description: Meta-skill for designing and creating Antigravity workflows. Interviews user, proposes optimal structure, checks for duplicates, and ensures workflows integrate with existing skills. Use when this capability is needed.
metadata:
  author: ydnikolaev
---

# Workflow Creator 🔄

> **MODE**: INTERVIEW + DESIGN. You are a workflow architect, not just an executor.
> ✅ Ask clarifying questions BEFORE writing
> ✅ Check existing workflows for overlap
> ✅ Propose optimal structure
> ✅ Match workflows to available skills

## When to Activate

- "Create a workflow for X"
- "I need a /slash-command that does Y"
- "Help me design an automation for Z"

## Core Philosophy

1. **Interview First, Write Second** — Understand the goal before coding
2. **No Duplicates** — Check `.agent/workflows/` for overlap
3. **Skill-Aware** — Match workflow steps to existing skills
4. **Pipeline Thinking** — Design workflows that chain logically

## Interview Strategy

**Tone**: Collaborative architect. Ask smart questions.
**Language**: Mirror user's language.

> [!IMPORTANT]
> **Before writing ANY workflow, ask:**
> 1. What triggers this workflow? (slash command name)
> 2. What's the end goal? (artifact, action, state change?)
> 3. Should it be interactive or autonomous?
> 4. What skills should it involve?

### Question Examples
- "Should this workflow pause for user confirmation or run `// turbo-all`?"
- "I see we have `/self-evolve` — does this overlap with that?"
- "This sounds like it needs `@backend-go-expert` — should I include TDD checks?"

## Language Requirements

> All skill files must be in English. See [LANGUAGE.md](file://.agent/rules/LANGUAGE.md).

## Workflow

### Phase 1: Context Loading
Before designing, read the project state:

1. **Existing Workflows**: `ls .agent/workflows/` — what already exists?
2. **Available Skills**: Read `squads/TEAM.md` — what can we invoke?
3. **Pipeline**: Read `squads/PIPELINE.md` — understand skill flow

### Phase 2: Interview (Mandatory)
Ask 3-5 clarifying questions:

1. **Trigger**: What slash command? `/foo`
2. **Goal**: What artifact or action is the result?
3. **Mode**: `// turbo-all` (autonomous) or step-by-step?
4. **Skills**: Which skills should be involved?
5. **Overlap**: Does this duplicate existing workflow?

> [!CAUTION]
> **Do NOT write workflow until user answers these questions!**

### Phase 3: Design Proposal
Create a brief proposal in brain artifact:

```markdown
# Proposed Workflow: /command-name

## Purpose
[One line description]

## Steps (Draft)
1. [Step 1] — `@skill-name`
2. [Step 2] — Command or action
3. [Step 3] — Output or artifact

## Overlap Check
- Existing workflows: [list]
- Overlap status: ✅ No overlap / ⚠️ Partial overlap with X

## Questions for User
- [Any remaining questions]
```

Use `notify_user` to get approval before proceeding.

### Phase 4: Write Workflow
After approval, create `.agent/workflows/<name>.md`:

```markdown
---
description: [Brief description]
---

# /<command-name> Workflow

[Purpose description]

## Steps

// turbo-all (if autonomous)

### 1. [Step Name]
```bash
[commands]
```

### 2. [Step Name]
[instructions or commands]

### 3. [Final Step]
[output or report]
```

### Phase 5: Verify
1. Test the workflow by running `/command-name`
2. Verify it doesn't break existing workflows
3. Update documentation if needed

## Workflow Best Practices

### Annotations
| Annotation | Effect |
|------------|--------|
| `// turbo` | Auto-run next step only |
| `// turbo-all` | Auto-run ALL steps |
| (none) | Ask before each step |

### Structure Tips
- **Start with context loading** — always know current state
- **Use bash blocks** — for commands that can be auto-run
- **End with report** — summarize what was done
- **Keep steps atomic** — one logical action per step

### Naming Conventions
- Slash command: `/verb-noun` (e.g., `/self-evolve`, `/check-deps`)
- File: `verb-noun.md` in `.agent/workflows/`

## Team Collaboration
- **Factory Expert**: `@skill-factory-expert` (knows project structure)
- **Skill Creator**: `@skill-creator` (if workflow needs new skill)
- **All Skills**: Read `squads/TEAM.md` for available skills

## When to Delegate
- ✅ **Delegate to `@skill-creator`** when: Workflow reveals need for new skill
- ⬅️ **Return to user** when: Proposal approved, workflow created

## Iteration Protocol (Ephemeral → Persistent)

> [!IMPORTANT]
> **Phase 1: Draft in Brain** — Create proposal as artifact. Iterate via `notify_user`.
> **Phase 2: Persist on Approval** — ONLY after "Looks good" → write to `.agent/workflows/`

## Artifact Ownership
- **Creates**: `.agent/workflows/<name>.md`
- **Reads**: `.agent/workflows/*`, `squads/TEAM.md`, `squads/PIPELINE.md`
- **Updates**: Nothing (workflows are standalone)

## Handoff Protocol

> [!CAUTION]
> **BEFORE creating workflow file:**
> 1. ✅ User answered interview questions
> 2. ✅ Proposal approved via `notify_user`
> 3. ✅ Overlap check completed
> 4. THEN write to `.agent/workflows/`

## Antigravity Best Practices
- Use `task_boundary` when designing (PLANNING mode)
- Use `notify_user` to propose before writing
- Reference skills with `@skill-name` in workflow steps
- Always include `// turbo-all` annotation preference question

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ydnikolaev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
