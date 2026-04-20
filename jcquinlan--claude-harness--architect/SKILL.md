---
name: architect
description: Design the domain architecture for a project. Produces architecture.md with data model, API surface, and file structure. Reads project.json for tech context. Use when this capability is needed.
metadata:
  author: jcquinlan
---

# Design Architecture

You are designing the technical architecture for a project. The user's project description is:

**$ARGUMENTS**

Your goal is to produce an `architecture.md` file that gives the implementer agent all the context it needs to build each feature without making ad-hoc architectural decisions.

## Step 1: Read Project Config

Read `project.json` if it exists. This tells you:
- What language, runtime, and framework the project uses
- Where source and test files live
- What the entry point is

If `project.json` does not exist, suggest running `/scaffold` first. The tech stack should already be decided before architecture design.

## Step 2: Understand the Domain

Read the project description carefully and identify:
- **Entities**: What are the core objects/nouns? (users, groups, items, etc.)
- **Actions**: What are the key verbs? (create, submit, approve, etc.)
- **Rules**: What constraints and business logic exist? (permissions, state machines, deadlines, etc.)
- **Roles**: Are there different user types with different permissions?

## Step 3: Design the Data Model

For each entity, define:
- **Fields** with types (string, integer, boolean, datetime)
- **Relationships** (belongs-to, has-many, many-to-many)
- **Constraints** (unique, not-null, foreign keys)
- **Indexes** for common queries

Draw the relationships in a simple text diagram. Example:

```
users 1──┬──N memberships N──┬──1 groups
          │                    │
          └──N songs N────────┘
```

## Step 4: Design the API Surface

For each major action, define the endpoint:

```
METHOD /path
  Auth: required | optional | none
  Input: { field: type, ... }
  Output: { field: type, ... }
  Errors: 400 (reason), 403 (reason), 409 (reason)
```

Group endpoints logically. Include both the happy path and error cases.

For web apps with UI, also note which pages are needed:
- Route, purpose, and whether it requires authentication
- Key UI elements (forms, lists, buttons)

## Step 5: Define the File Structure

Map out which files will exist and what each one is responsible for. Use the language and conventions from `project.json`:

```
project/
├── <entry_point>     # Main entry point
├── <src_dir>/
│   ├── routes/       # HTTP route handlers
│   ├── models/       # Data models / entities
│   └── services/     # Business logic
├── <test_dir>/       # Test files
├── project.json      # Tech stack config
└── prd.json          # Feature spec
```

Each file should have a single, clear responsibility. Prefer flat structure over deep nesting for small projects.

## Step 6: Identify State Machines

If entities go through discrete states (e.g., "forming" → "ready" → "active" → "completed"), document:
- All valid states
- Valid transitions (from → to)
- What triggers each transition
- Who can trigger it (roles/permissions)

## Step 7: Call Out Key Decisions

Document any non-obvious architectural decisions and their rationale:
- Why synchronous over async (or vice versa)
- Why one pattern over another
- Security considerations
- Scalability notes (or explicit "not a concern for v1")
- What is intentionally deferred (not in scope)

## Output

Write `architecture.md` in the current working directory with all of the above. Use clear headings and keep it scannable — the implementer agent will read this at the start of every session.

After writing, summarize the key decisions for the user and ask if anything should change before proceeding to `/create-prd`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcquinlan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
