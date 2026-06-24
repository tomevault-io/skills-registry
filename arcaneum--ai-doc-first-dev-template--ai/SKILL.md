---
name: ai-doc-first-framework
description: Automatically maintain documentation-as-memory in AI-assisted development projects. This skill detects repos using the ai-doc-first-framework structure and maintains PROGRESS.md, DECISIONS.md, ARCHITECTURE.md, and PRD.md as persistent AI memory. Use this skill when working in any repository that contains a docs/ folder with these documentation files. Use when this capability is needed.
metadata:
  author: arcaneum
---

# AI Documentation-First Framework Skill

This skill enables AI assistants to maintain their own persistent memory across sessions using documentation-as-memory. It automatically detects repositories using the ai-doc-first-framework structure and operates within that system.

## When This Skill Activates

This skill automatically activates when working in a repository that contains:
- `docs/PROGRESS.md` - What's already built
- `docs/DECISIONS.md` - What's already decided
- `docs/ARCHITECTURE.md` - How the system is structured
- `docs/PRD.md` - What you're building and why

If these files don't exist yet, you can help the user initialise the framework.

## Core Behaviour: Context Engineering

**Your role with this skill:**
1. **Read documentation at session start** - Before responding to the first request, read PROGRESS.md and DECISIONS.md to understand context
2. **Reference past decisions** - Before suggesting changes, check if a decision has already been made
3. **Maintain documentation as you work** - Update docs automatically when you build features or make decisions
4. **Stay consistent** - Never contradict documented decisions unless explicitly asked to reconsider

## Initialisation (First-Time Setup)

When a user clones the ai-doc-first-framework template or asks to set up the framework:

### Step 1: Verify Framework Files Exist

Check for the presence of:
```
docs/
├── PRD.md
├── ARCHITECTURE.md
├── PROGRESS.md
└── DECISIONS.md
```

If they don't exist, offer to create them.

### Step 2: Initial Context Gathering

Help the user fill in `docs/PRD.md` by asking:
- What are you building?
- Why does this project exist?
- What problem does it solve?
- What's in scope and what's out of scope?

### Step 3: Review Existing Code (if any)

If the repo already has code:
- Scan the codebase structure
- Document current architecture in `docs/ARCHITECTURE.md`
- Log existing patterns and conventions
- Add initial entry to `docs/PROGRESS.md` noting what's already present

### Step 4: Confirm Framework Active

Let the user know:
- The framework is initialised
- You'll maintain these docs as you work
- They should review what you document
- Future sessions will build on this context

## Session Start Protocol

**At the beginning of EVERY session:**

1. **Read `docs/PROGRESS.md`** - Understand what's been built
2. **Read `docs/DECISIONS.md`** - Understand what's been decided
3. **Scan `docs/ARCHITECTURE.md`** - Understand current structure
4. **Reference `docs/PRD.md`** - Understand project goals

**Then summarize what you learned:**
```
I've reviewed the documentation. Here's the current state:

From PROGRESS.md:
- Latest work: [last entry]
- Current state: [summary]

From DECISIONS.md:
- Key decisions: [relevant recent decisions]

From ARCHITECTURE.md:
- Structure: [high-level overview]

Ready to continue. What would you like to work on?
```

This confirms you have context and shows the user you're operating within the framework.

## During Work: Automatic Documentation

As you complete work, **automatically update documentation**:

### When You Build a Feature

Update `docs/PROGRESS.md`:
```markdown
## YYYY-MM-DD — T-XXX
- Implemented [feature name] in [file paths]
- Added [specific functionality]
- [Any relevant notes]
```

### When You Make a Decision

Update `docs/DECISIONS.md`:
```markdown
## D-XXX — [Decision Title]

**Context**
[Why this decision needed to be made]

**Decision**
[What you chose]

**Reasoning**
- [Why this choice over alternatives]
- [Key factors that influenced the decision]

**Consequences**
- [Positive outcomes]
- [Trade-offs or limitations]
```

### When Structure Changes

Update `docs/ARCHITECTURE.md`:
- Add new components or modules
- Document how pieces connect
- Update patterns or conventions
- Note key dependencies

## Before Making Suggestions

**Always check documentation first:**

1. **Check DECISIONS.md** - Has this been decided already?
   - If yes: Respect the decision unless asked to reconsider
   - Reference the decision: "I see from D-012 we chose PostgreSQL..."

2. **Check ARCHITECTURE.md** - Does this fit the current structure?
   - Suggest changes that align with existing patterns
   - Don't randomly introduce new architectures

3. **Check PROGRESS.md** - Has this been built already?
   - Don't suggest rebuilding existing features
   - Build on what's already there

**Example:**
```
User: "Should we add a database?"

You: "I see from DECISIONS.md (D-008) that we chose PostgreSQL
for data storage because we need ACID compliance. From PROGRESS.md,
the database schema is already defined in db/schema.sql.

Would you like me to help extend the existing schema or
work on something else?"
```

## Documentation Standards

### PROGRESS.md Format
- Date in YYYY-MM-DD format
- Sequential task numbers (T-001, T-002...)
- Brief summary of what was done
- File paths where changes occurred
- Keep entries concise but clear

### DECISIONS.md Format
- Sequential decision numbers (D-001, D-002...)
- Clear decision title
- Context explaining why decision was needed
- The actual decision made
- Reasoning with bullet points
- Consequences (both positive and trade-offs)

### ARCHITECTURE.md Format
- High-level system overview
- Key components and their responsibilities
- How components interact
- Important patterns and conventions
- Technology choices and why
- Update incrementally as structure evolves

### PRD.md Format
- Project purpose and goals
- Problem being solved
- Target users
- In scope / out of scope
- Success criteria
- Updated only when project direction changes

## What NOT to Document

Don't update documentation for:
- Typo fixes
- Code formatting changes
- Comment updates
- Trivial refactors
- Dependency version bumps (unless significant)

Only document **meaningful work** - features, decisions, architectural changes.

## Handling Conflicts

If the user asks you to do something that contradicts a documented decision:

1. **Reference the decision:**
   "I see from DECISIONS.md (D-015) we chose approach X because [reasoning]."

2. **Ask for confirmation:**
   "Would you like to reconsider that decision, or should we stick with the current approach?"

3. **If reconsidered:**
   - Update DECISIONS.md with the new decision
   - Note it's a reversal of previous decision
   - Document why it changed

## Working Without the Framework

If a repository doesn't have this structure, you can still work normally. This skill only activates when the framework files are present.

If the user wants to add the framework to an existing project, offer to:
1. Create the four core documentation files
2. Document existing architecture
3. Log the current state
4. Start maintaining docs going forward

## Example Session Flow

**Session Start:**
```
User: "Add user authentication"

You: [Read docs/PROGRESS.md and docs/DECISIONS.md]

You: "I've reviewed the documentation. I can see we're building
a SaaS dashboard (from PRD.md) and have already implemented
the user registration form (from PROGRESS.md, T-008).

From DECISIONS.md (D-012), we chose JWT for auth because
the mobile app needs stateless authentication.

I'll build on the existing registration system and implement
JWT-based authentication. Should I proceed?"

User: "Yes"

You: [Build the feature]

You: [Update docs/PROGRESS.md with T-009 entry]

You: [Update docs/DECISIONS.md if new decisions were made]

You: [Update docs/ARCHITECTURE.md with auth flow]

You: "Authentication implemented. I've updated:
- PROGRESS.md (T-009): Added JWT auth in auth/jwt.service.ts
- DECISIONS.md (D-018): Chose bcrypt for password hashing
- ARCHITECTURE.md: Documented auth flow and middleware

Ready for next task."
```

**Next Session (Days Later):**
```
User: "Add social auth"

You: [Read documentation first]

You: "I can see from DECISIONS.md (D-012, D-018) we're using
JWT tokens with bcrypt password hashing. From PROGRESS.md (T-009),
the auth system is in auth/jwt.service.ts.

I'll extend the existing JWT system to support OAuth providers.
Which providers would you like to add?"
```

## Benefits of This Approach

**For the AI (You):**
- Persistent memory across sessions
- Context continuity
- Fewer contradictions
- Better suggestions aligned with past decisions

**For the User:**
- Less time re-explaining context
- Better AI sessions immediately
- Clear audit trail of what was built and why
- Humans can read the same docs for onboarding

## Key Principles

1. **Read before you act** - Start every session by reading PROGRESS.md and DECISIONS.md
2. **Document as you work** - Update docs when you build features or make decisions
3. **Reference before suggesting** - Check if decisions have been made before proposing alternatives
4. **Stay consistent** - Don't contradict documented choices without explicit user request
5. **Keep it concise** - Documentation should be clear and brief, not novels

## Framework Repository

This framework is open source:
- **Template repo:** https://github.com/arcaneum/ai-doc-first-dev-template
- **License:** MIT
- **Works with:** Claude Code, Cursor, Copilot, Gemini, any AI assistant

## Summary

You are an AI assistant working within a documentation-as-memory framework. You maintain persistent context across sessions by:
- Reading docs at session start
- Documenting what you build
- Referencing past decisions
- Staying consistent with documented choices

This is AI creating its own external memory. The user reviews and approves what you document, but you do the documentation work.

Your goal: Be genuinely useful across sessions by maintaining continuity through documentation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/arcaneum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
