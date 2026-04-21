---
name: prd
description: Create prd.json files for Ralph Wiggum autonomous AI coding loops. Use when user wants to create a PRD, plan features for Ralph, define tasks for autonomous coding, or prepare work for AFK agent loops. Use when this capability is needed.
metadata:
  author: deloreyj
---

Create structured `prd.json` files for Ralph Wiggum autonomous coding loops. Ralph runs AI agents in a loop, letting them work autonomously on tasks until complete.

## PRD Format

Ralph expects `prd.json` with this structure:

```json
{
  "name": "Feature/Project Name",
  "description": "Brief overview of what this PRD accomplishes",
  "items": [
    {
      "id": "unique-id",
      "category": "functional|technical|integration|testing|documentation",
      "description": "Clear, specific task description",
      "steps": ["Step 1 to verify completion", "Step 2 to verify completion"],
      "passes": false,
      "priority": "high|medium|low",
      "risk": "high|medium|low"
    }
  ]
}
```

## Task Granularity (Critical)

**Small steps = higher quality.** Context windows degrade with length (context rot). Each iteration has startup costs, but smaller tasks produce better code.

Guidelines:

- One logical change per task
- Each task completable in <30 min of agent work
- Verification steps should be concrete and testable
- If task feels large, break into subtasks

Bad: "Implement user authentication"
Good:

- "Create User schema with email, passwordHash, createdAt fields"
- "Add POST /auth/register endpoint with email validation"
- "Add POST /auth/login endpoint returning JWT"
- "Add auth middleware checking JWT on protected routes"

## Task Prioritization

Ralph chooses tasks autonomously. Guide priority with `priority` and `risk` fields:

1. **High risk + High priority**: Architectural decisions, core abstractions
2. **High risk + Medium priority**: Integration points between modules
3. **Medium risk**: Unknown unknowns, spike work
4. **Low risk**: Standard features, polish, quick wins

Fail fast on risky work. Save easy wins for later.

## Categories

- `functional`: User-facing features, UI components
- `technical`: Backend logic, data models, APIs
- `integration`: Connecting modules, external services
- `testing`: Test coverage, verification
- `documentation`: READMEs, inline docs (use sparingly)

## Verification Steps

Steps must be concrete. Ralph marks `passes: true` when verified.

Bad steps:

- "Make sure it works"
- "Test the feature"

Good steps:

- "Run `npm test` - all tests pass"
- "POST /api/users returns 201 with valid payload"
- "Click 'Submit' button navigates to /dashboard"
- "TypeScript compiles with no errors"

## Creation Process

1. **Understand scope**: What is "done"? Be explicit to prevent shortcuts.
2. **List features**: Break down into atomic tasks.
3. **Order by risk**: Put architectural/integration work first.
4. **Write verification**: Each task needs testable completion criteria.
5. **Set priorities**: Guide Ralph's task selection.

## Example PRD

```json
{
  "name": "User Settings Page",
  "description": "Add settings page where users can update profile and preferences",
  "items": [
    {
      "id": "settings-schema",
      "category": "technical",
      "description": "Create UserSettings schema with theme, notifications, timezone fields",
      "steps": [
        "Schema defined in src/db/schema.ts",
        "TypeScript types exported",
        "Migration generated with drizzle-kit"
      ],
      "passes": false,
      "priority": "high",
      "risk": "medium"
    },
    {
      "id": "settings-api",
      "category": "technical",
      "description": "Add GET/PUT /api/settings endpoints",
      "steps": [
        "GET /api/settings returns current user settings",
        "PUT /api/settings updates and returns updated settings",
        "Invalid payload returns 400 with validation errors"
      ],
      "passes": false,
      "priority": "high",
      "risk": "low"
    },
    {
      "id": "settings-ui",
      "category": "functional",
      "description": "Create Settings page component with form for all settings fields",
      "steps": [
        "Settings page renders at /settings route",
        "Form displays current values from API",
        "Submit button saves changes via PUT endpoint",
        "Success toast appears after save"
      ],
      "passes": false,
      "priority": "medium",
      "risk": "low"
    },
    {
      "id": "settings-tests",
      "category": "testing",
      "description": "Add tests for settings API endpoints",
      "steps": [
        "Test file exists at src/worker/__tests__/settings.test.ts",
        "Tests cover GET, PUT success, and validation errors",
        "npm run test:worker passes"
      ],
      "passes": false,
      "priority": "medium",
      "risk": "low"
    }
  ]
}
```

## Anti-Patterns

- **Vague tasks**: "Improve performance" - no clear completion criteria
- **Mega tasks**: "Build the dashboard" - too large, break down
- **Missing verification**: No steps = Ralph decides when "done"
- **Polish first**: UI tweaks before core logic is stable
- **No risk ordering**: Easy wins before architecture

## Integration with Ralph

Ralph's prompt instructs it to:

1. Choose highest priority incomplete task
2. Implement the feature
3. Run relevant checks (tests, types, lint, build)
4. Fix any issues
5. Mark `passes: true` when verified
6. Commit only source code (not prd.json)

The PRD is a living document - adjust `passes` back to `false` if implementation is wrong, add new items mid-loop as needed.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/deloreyj) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
