---
name: am2rican5spec-writer
description: Guide creation of functional specifications with progressive detail elicitation. Produces structured specs with Given/When/Then acceptance criteria, edge cases, constraints, and rigor level annotation. Use when user says "write a spec", "create specification", "spec for", "define requirements", "acceptance criteria for", "specify behavior", or "what should this feature do". Do NOT use for implementing code from specs (use sdd-workflow) or validating implementation against specs (use spec-validator agent). Use when this capability is needed.
metadata:
  author: am2rican5
---

# Spec Writer

## Critical Rules

- NEVER overwrite an existing spec without reading it first and showing the user what will change
- ALWAYS use Given/When/Then format for acceptance criteria — no exceptions
- ALWAYS start with the minimum viable spec (purpose + 2-3 criteria) before going deeper — progressive disclosure, not interrogation
- WHEN the user's description is vague, ask targeted clarifying questions — do not guess at requirements
- ALWAYS validate the spec file exists after writing it
- NEVER include implementation details in specs — specs describe WHAT, not HOW

## Instructions

### Step 1: Determine Create or Update

1. IF `$ARGUMENTS` contains a path to an existing spec file → read it and go to **Step 7 (Iteration)**
2. IF a spec already exists at `specs/<feature-name>.md` for the described feature → read it and go to **Step 7 (Iteration)**
3. OTHERWISE → proceed to **Step 2 (Elicit Purpose)**

### Step 2: Elicit Purpose

Ask the user to describe the feature. From their response, establish:

- **What** the feature does (core behavior)
- **Who** uses it (user role or system actor)
- **What success looks like** (the key outcome)

IF the description is vague (e.g., "make it faster", "improve the UX", "fix auth"):
- Ask targeted questions:
  - "What is the current behavior?"
  - "What is the desired behavior?"
  - "Who is affected?"
  - "What does 'done' look like?"
- Do NOT proceed until the purpose is clear enough to write acceptance criteria.

Draft a 1-2 sentence **Purpose** statement and confirm with the user.

### Step 3: Draft Acceptance Criteria

From the purpose and user's description, generate 2-5 acceptance criteria in Given/When/Then format:

```
### Acceptance Criteria

#### AC1: <descriptive name>
- **Given** <precondition>
- **When** <action or trigger>
- **Then** <expected outcome>
```

Present the draft to the user and ask:
> "Here are the initial acceptance criteria. Are these correct? Anything to add, change, or remove?"

Iterate until the user confirms.

### Step 4: Edge Case Elicitation

Based on the feature type, proactively suggest relevant edge cases. Do NOT rely solely on the user to enumerate them.

**For input validation features**, suggest:
- Empty input / missing required fields
- Maximum length / boundary values
- Special characters / injection attempts
- Invalid types / format mismatches
- Concurrent submissions

**For CRUD operations**, suggest:
- Resource not found
- Resource already exists (duplicate)
- Partial failure / transaction rollback
- Permission denied / unauthorized access
- Concurrent modification / race conditions

**For API/integration features**, suggest:
- Network timeout / unavailable service
- Rate limiting / throttling
- Invalid response format
- Authentication expiry / token refresh
- Partial success in batch operations

**For auth/security features**, suggest:
- Invalid credentials
- Expired session / token
- Privilege escalation attempts
- Brute force / lockout thresholds
- Cross-tenant data access

Present suggested edge cases as a checklist. Let the user accept, reject, or modify each individually. Add accepted edge cases to the spec.

### Step 5: Constraints and Dependencies

Ask the user about:
- **Technical constraints**: "Are there any technical constraints? (e.g., must use existing middleware, no new dependencies, specific language/framework)"
- **Non-functional requirements**: "Any performance, security, or accessibility requirements?"
- **Dependencies**: "Does this depend on other features, services, or data?"

Only include sections the user has content for — skip empty sections.

### Step 6: Rigor Level Selection

Select the appropriate rigor level:

| Level | When to Use | What It Means |
|-------|------------|---------------|
| **spec-first** | Prototypes, one-off features, small changes | Spec guides initial dev; may not be maintained |
| **spec-anchored** | APIs, contracts, long-lived features, team projects | Spec maintained alongside code; both evolve together |
| **spec-as-source** | Generated code, infrastructure-as-code | Spec IS the source; code regenerated from spec |

**Default to spec-first** unless the user's context suggests otherwise.

**Suggest upgrading** to spec-anchored if the user mentions: "API", "contract", "long-term", "team", "shared", "public interface", "breaking change".

Include the rigor level and a one-line rationale in the spec.

### Step 7: Write the Spec File

Write the spec to `specs/<feature-name>.md` in the project root:

```markdown
# Spec: <Feature Name>

## Purpose
<1-2 sentence purpose statement>

## Rigor Level
**<spec-first | spec-anchored | spec-as-source>** — <one-line rationale>

## Acceptance Criteria

#### AC1: <name>
- **Given** <precondition>
- **When** <action>
- **Then** <outcome>

#### AC2: <name>
...

## Edge Cases

- <edge case 1>: <expected handling>
- <edge case 2>: <expected handling>
...

## Constraints
<technical constraints, if any>

## Dependencies
<dependencies on other features/services, if any>
```

Create the `specs/` directory if it doesn't exist. Confirm the file was written successfully.

### Step 8: Iteration Support (for existing specs)

When updating an existing spec:

1. Read the existing spec file and present it to the user
2. Ask: "What needs to change? (add criteria, modify existing, add edge cases, update constraints)"
3. For **adding** criteria: append new criteria without modifying existing ones
4. For **modifying** criteria: show the old and new version side-by-side before applying
5. For **conflicts**: if new criteria contradict existing ones, flag the conflict and ask the user to resolve
6. Preserve all unchanged sections

## Examples

### Example 1: Clear Feature Request

**User**: "Write a spec for user password reset via email"

**Spec Writer output** (after elicitation):
```markdown
# Spec: Password Reset via Email

## Purpose
Allow users to reset their forgotten password by receiving a time-limited reset link via email.

## Rigor Level
**spec-anchored** — Security-critical auth flow shared across teams

## Acceptance Criteria

#### AC1: Request reset link
- **Given** a registered user on the login page
- **When** they enter their email and click "Forgot Password"
- **Then** a reset email is sent with a unique, time-limited link

#### AC2: Reset password with valid link
- **Given** a user with a valid (unexpired) reset link
- **When** they click the link and enter a new password
- **Then** their password is updated and they are redirected to login

#### AC3: Expired link handling
- **Given** a user with an expired reset link (>1 hour old)
- **When** they click the link
- **Then** they see an error message and option to request a new link

## Edge Cases
- Non-existent email: Show same success message (prevent email enumeration)
- Multiple reset requests: Invalidate previous links when a new one is generated
- Password reuse: Optionally enforce that new password differs from current
- Concurrent sessions: Existing sessions should remain valid after password reset

## Constraints
- Must use existing email service
- Reset links must expire after 1 hour
- Must not reveal whether an email is registered

## Dependencies
- Email service (existing)
- User authentication system (existing)
```

### Example 2: Vague Request

**User**: "Make search better"

**Spec Writer response**:
> Before I can write a spec, I need to understand what "better" means:
> 1. What is the current search behavior?
> 2. What specific problems are users experiencing?
> 3. What does "better" look like — faster results, more relevant results, better UI?
> 4. Who are the primary users of search?

## Troubleshooting

### User can't articulate requirements
**Cause:** They know what they want but struggle to express it formally.
**Solution:** Try asking "What would you demo to stakeholders?" or "What should a user be able to do that they can't today?" These reframe the question from abstract requirements to concrete outcomes.

### Feature is too large for one spec
**Cause:** The feature spans multiple concerns, roles, or subsystems.
**Solution:** Break it into sub-features, each with its own spec file. Reference the parent feature in the Purpose section of each sub-spec.

### User wants implementation details in spec
**Cause:** They're thinking about the "how" before the "what".
**Solution:** Redirect: "Specs describe WHAT, not HOW. Implementation details belong in the planning phase." If they insist a constraint is important (e.g., "must use Redis"), capture it in the Constraints section.

### Existing spec is very old or stale
**Cause:** The spec was written long ago and the feature has evolved.
**Solution:** Ask the user if they want to start fresh or update incrementally. If stale, consider starting fresh with the old spec as reference rather than trying to patch it.

### Edge cases seem endless
**Cause:** Complex features have many failure modes.
**Solution:** Focus on the top 5-7 most impactful edge cases. Note others as "future considerations" if the user wants to capture them without bloating the spec.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/am2rican5) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
