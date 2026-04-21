---
name: spec-design
description: Design robust specs through principle-driven interviews, then create GitHub issues with sub-issue hierarchies. Use when starting a new feature, designing a solution, or when user says "let's spec this out", "design this feature", "create a spec". Use when this capability is needed.
metadata:
  author: ainergiz
---

# Spec Design & Issue Creation

Conduct principle-driven interviews to create comprehensive specs, then create GitHub issue hierarchies.

## Workflow Overview

```
Phase 1: Discovery Interview  → Read references/interview-guide.md
Phase 2: Principles Summary   → Confirm decisions with user
Phase 3: Write Spec          → Read references/spec-template.md
Phase 4: Review Spec         → User reviews .context/specs/<issue>.md
Phase 5: Create Issues       → Use gh-issues skill
```

## Phase 1: Discovery Interview

**If no initial context:** Ask user what they want to spec out.

**Interview approach:**

- Use AskUserQuestion tool throughout
- Ask 2-4 questions at a time
- Go deep on answers, follow up on interesting points
- Challenge assumptions respectfully
- Reference the user problem throughout

**Core principles to weave in:**

- **YAGNI**: Build only what's needed NOW
- **Simplicity**: Simplest viable approach
- **Boring Tech**: Proven over trendy
- **User Problem**: Every decision traces back to it

→ Read [references/interview-guide.md](references/interview-guide.md) for detailed questions.

### RFC Detection

Watch for signals needing broader review:

- Breaking API changes
- Significant database migrations
- New architectural patterns
- Cross-team dependencies

If detected: "This seems significant. Should we generate an RFC template instead?"

## Phase 2: Principles Summary

Before writing spec, confirm with user:

```
Before I write the spec, let's confirm:

✓ USER PROBLEM: [one-line summary]
✓ YAGNI - NOT building: [out of scope items]
✓ SIMPLICITY: [approach and why it's simplest]
✓ TECHNOLOGY: [choices with reasons]
✓ COMPLEXITY:
  - Essential: [must have]
  - Incidental: [choosing to add + justification]
✓ RISKS: [risk → mitigation]

Does this capture our decisions?
Options: ["Yes, write the spec", "Let me clarify"]
```

## Phase 3: Write Spec

→ Read [references/spec-template.md](references/spec-template.md) for the template.

Write spec to `.context/specs/<feature-name>.md` (or `<issue-number>.md` if known).

**Sub-task splitting guidelines:**

- Each sub-issue = SMALLEST LOGICAL UNIT
- INDEPENDENT: can ship without other sub-issues
- VERIFIABLE: clear "done" state, testable in isolation
- Consider: data layer → API layer → UI layer
- Consider: core functionality → edge cases → polish

## Phase 4: Review Spec

1. Tell user: "Spec written to `.context/specs/<name>.md` - please review"
2. Open: `open .context/specs/<name>.md`
3. Ask: "Ready to create GitHub issues?"
   - Options: ["Yes, create issues", "I'll edit first", "Cancel"]

If user edits, re-read the file before proceeding.

## Phase 5: Create Issues

Ask about assignment:

- Options: ["Assign to me", "Leave unassigned"]

**Create main issue:**

```bash
gh issue create --title "[Feature Name]" --body "$(cat .context/specs/<name>.md)"
```

**Create sub-issues:** Use `gh-issues` skill (see references/hierarchy.md).

For each sub-issue, confirm with user:

```

Proposed sub-issue [1/N]:
Title: [title]
Body: [context, scope, depends on, acceptance criteria]

Create this?
Options: ["Yes", "Skip", "Modify"]

```

Use parallel subagents for independent sub-issues.

## Phase 6: Summary

```

## Created Issues

Main: #[number] - [title]
├── #[sub1] - [title] (ready)
├── #[sub2] - [title] (blocked by #sub1)
└── #[sub3] - [title] (blocked by #sub1)

Dependency order:

1. #[sub1] - start now
2. #[sub2], #[sub3] - after #sub1

Run `/load-issue [number]` to start working.

```

## Notes

- Use current repository
- Use gh-issues skill for sub-issue creation (native GitHub sub-issues)
- Include enough context in each sub-issue for independent agent work
- Keep USER PROBLEM central throughout

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ainergiz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
