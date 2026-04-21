---
name: write-local-user-stories
description: Write or edit local user story markdown files that sync with Azure DevOps. Use when creating new user stories, editing existing stories, or when asked to write acceptance criteria. Stories are stored in docs/user-stories/{id}.md and synced via MCP sync tools. Use when this capability is needed.
metadata:
  author: klemensms
---

# Writing Local User Stories

User stories sync between local markdown files and Azure DevOps. Follow this exact format for successful push/pull cycles.

## Fixing Existing Stories (Auto-Fix Mode)

When pointed at an existing user story file, **automatically fix all formatting issues** without asking. Apply these fixes:

### Placeholder Text → TODO Badges

| Find | Replace With |
|------|--------------|
| `(To be defined)` | `![Static Badge](https://img.shields.io/badge/TODO-Smartimpact-blue)` |
| `[To be defined]` or similar | `![Static Badge](https://img.shields.io/badge/TODO-Smartimpact-blue)` |

### Example Reminders - DO NOT REMOVE

**Keep these helpful reminder patterns** - they list commonly forgotten items:
```markdown
- (e.g.: SPO set up, Customer Insights configured, outgoing mailboxes defined & available, etc.)
```

These reminders should only be removed:
- By the consultant explicitly
- When the agent is instructed to finalize/clean up the story
- When actual prerequisites are added to replace them

If the section has ONLY the example reminder (no real content), add a TODO badge ABOVE it:
```markdown
## Prerequisites

![Static Badge](https://img.shields.io/badge/TODO-Smartimpact-blue)
- (e.g.: SPO set up, Customer Insights configured, outgoing mailboxes defined & available, etc.)
```

### ADO Links → Simple Format

| Find | Replace With |
|------|--------------|
| `[#1234](https://dev.azure.com/...)` | `#1234` |
| `[#1234](full-ado-url)` | `#1234` |

ADO auto-links `#ID` format and shows title/status - no need for full URLs.

### Other Auto-Fixes

- Remove empty bullet points (lines with just `- `)
- Fix heading levels (content sections must use `##`, subsections `###`)
- Ensure Given/When/Then keywords are bold
- Remove extra blank lines between Given/When/Then/And lines
- Fix TODO badges that are inline with headings (move to next line)

### After Fixing

1. Show a summary of changes made
2. Remind to sync using `/ado-push-items <id>` or the `sync-work-item-from-file` MCP tool

## File Location

`docs/user-stories/{id}.md` where `{id}` is the ADO work item ID.

## Required Structure

```markdown
---
id: <work-item-id>
title: <title>
state: <state>
assignedTo: <name>
storyPoints: <number>
parent: <parent-id>
moscow: <Must|Should|Could|Won't>
tags:
- <tag1>
areaPath: <area-path>
lastSyncedRevision: <revision-number>
---

# Description

## Client Requirement

<User story in "As a... I want... so that..." format>

### Process Diagrams

<Links to Figma/diagrams>

### Assumptions

- <assumption 1>
- <assumption 2>

## Proposed Technical Approach

<Technical description>
- <implementation detail 1>
- <implementation detail 2>

## Prerequisites

- #<id> - <description>

---

# Acceptance Criteria

## Business Rules

| Rule | Description |
|---|---|
| <rule name> | <description> |

## Feature: <Feature Name>

### AC1: <Title>

**Given** <precondition>
**When** <action>
**Then** <expected result>
**And** <additional result>

### AC2: <Title>

**Given** <precondition>
**And** <additional precondition>
**When** <action>
**Then** <expected result>
```

## Empty Acceptance Criteria

When no acceptance criteria or business rules have been defined:
- **Do NOT add placeholder content** to the Acceptance Criteria field
- **Leave it completely empty** - omit the entire `# Acceptance Criteria` section
- This allows filtering in ADO to identify stories missing ACs

Example of a story WITHOUT acceptance criteria:
```markdown
---
id: 1234
...
---

# Description

## Client Requirement

**As a** user...

## Proposed Technical Approach
![Static Badge](https://img.shields.io/badge/TODO-Smartimpact-blue)

## Prerequisites
![Static Badge](https://img.shields.io/badge/TODO-Smartimpact-blue)
```

Note: No `---` divider or `# Acceptance Criteria` section when ACs are empty.

## Acceptance Criteria Scope

**Only write ACs for custom behavior we are building—not for what Dynamics CRM provides out-of-the-box (OOTB).**

### DO NOT Write ACs For (OOTB Functionality)

Dynamics handles these automatically—testing them wastes effort and bloats the AC list:

| OOTB Capability | Why We Don't Test It |
|-----------------|----------------------|
| Creating/saving records | Standard Dataverse operation |
| Required field validation | Configured at column level |
| Standard navigation to forms | Model-driven app behavior |
| Basic CRUD operations | Platform capability |
| Form field visibility | Configured in form designer |
| Standard lookups/relationships | Dataverse handles referential integrity |
| Opening a form based on Figma design | If the form exists, users can open it |

### DO Write ACs For (Custom Behavior)

Only test what the user story or design **specifically calls out** as custom:

| Custom Behavior | Example |
|-----------------|---------|
| Custom validations | "Membership number must match pattern MBR-NNNNNN" |
| Business rules with specific logic | "If grade is Student, end date must fall within academic year" |
| RBAC beyond standard security roles | "Branch Reps can only see members in their branch" |
| Calculated/rollup fields | "Total subscriptions = sum of active membership fees" |
| Integration behavior | "When status changes to Approved, trigger sync to SQL" |
| Workflow/Flow triggers | "Email sent within 24 hours of enquiry creation" |
| Custom UI behavior | "Grade dropdown filters based on selected Class" |
| Auto-population/defaulting | "Region auto-populated based on postcode lookup" |

### Anti-Pattern Example

**BAD** - Testing OOTB functionality:
```markdown
### AC1: Creating a New Enquiry

**Given** a user with access to the Enquiry form
**When** they submit the form with valid data
**Then** a new record is saved in the Enquiry table
**And** all required fields are validated before saving
```

This tests nothing custom—Dynamics does all of this automatically for any table.

**GOOD** - Testing custom behavior from the same story:
```markdown
### AC1: Enquiry Auto-Assignment

**Given** a new Enquiry is created
**When** the member's branch is identified
**Then** the Enquiry is automatically assigned to the Branch Secretary
**And** an email notification is sent to the assignee

### AC2: Enquiry Reference Number

**Given** a new Enquiry is saved
**When** the record does not yet have a reference number
**Then** a unique reference number is generated in format ENQ-YYYYMMDD-NNNN
```

### The Litmus Test

Before writing an AC, ask: **"Would this behave differently if we didn't build anything custom?"**

- **YES** → Write the AC (we're testing our custom work)
- **NO** → Don't write it (Dynamics handles it OOTB)

If a user story is purely "create a new table with these fields," and there's no custom logic, validation rules, or automation—**the story may not need any ACs at all**. The table either exists with the right columns or it doesn't. Use the "Empty Acceptance Criteria" guidance above.

## Template Placeholders

User stories often contain template placeholders that remind consultants to consider certain aspects. These are **NOT confirmed requirements** - they are prompts for Smartimpact consultants to consider.

Common patterns:
```markdown
**Data Migration** (optional)
[Consider: treating "existing/old" data/records differently]

**Prerequisites (optional)**
[e.g.: SPO set up, Customer Insights configured, outgoing mailboxes defined & available, etc.]
```

When you encounter these:
1. **Do NOT treat them as confirmed requirements**
2. **Think about whether they apply** to this specific user story
3. **These are for Smartimpact consultants** to evaluate - NOT questions for the client
4. **Keep them as reminders** in the Assumptions section for the consultant to address later
5. **Remove or replace placeholder text** only when the consultant has confirmed whether they apply
6. If after evaluation a question needs client input, THEN add the client question badge

Example of keeping a placeholder as a consultant reminder:
```markdown
### Assumptions

- Data Migration: Consider whether existing/old records need different handling
```

## Prerequisites Section

**Never remove the Prerequisites section** - even when prerequisites are not yet confirmed:
- Keep the section with a TODO badge (the badge itself indicates work is needed - no placeholder text required)
- Think about what needs to be in place before this user story can be delivered
- Consider: other user stories, system configuration, external dependencies, data setup

Example:
```markdown
## Prerequisites
![Static Badge](https://img.shields.io/badge/TODO-Smartimpact-blue)
```

Common prerequisites to consider:
- Other user stories that must be completed first (use `#ID` format, e.g., `#1065`)
- SharePoint Online configuration
- Customer Insights setup
- Outgoing mailboxes defined and available
- Security roles configured
- Environment variables set
- Connection references established

## Critical Formatting Rules

1. **Marker headings use `#`**: `# Description` and `# Acceptance Criteria` are marker headings that indicate ADO field boundaries (not pushed to ADO itself)
2. **Main sections use `##`**: Client Requirement, Proposed Technical Approach, Prerequisites, Business Rules, Feature:
3. **Subsections use `###`**: Process Diagrams, Assumptions, AC1:, AC2:, etc.
4. **Given/When/Then keywords must be bold**: `**Given**`, `**When**`, `**Then**`, `**And**`, `**Or**`
5. **No blank lines** between Given/When/Then/And lines within an AC
6. **AC divider**: Use `---` on its own line before `# Acceptance Criteria`
7. **ADO work item links**: Use `#1234` format - ADO auto-links and displays title/status

## Visual Markers

Use these badges and styles to highlight important information:

**Questions for the client** - Add badge at the END of items needing client confirmation:
```markdown
Text needing confirmation ![Static Badge](https://img.shields.io/badge/Question-Client-yellow)
```

**TODO for Smartimpact** - Add on the line BELOW the section header when work is not yet done. The badge itself indicates work is needed - no placeholder text like "(To be defined)" required:
```markdown
## Proposed Technical Approach
![Static Badge](https://img.shields.io/badge/TODO-Smartimpact-blue)
```

**Critical information** - Use for warnings, blockers, or must-read content:
```markdown
<span style="color:red">Critical Information Here</span>
```

Example usage:
```markdown
## Proposed Technical Approach
![Static Badge](https://img.shields.io/badge/TODO-Smartimpact-blue)

### Assumptions

- Data Migration: Consider whether existing/old records need different handling
- <span style="color:red">Pending - WIP</span> This section requires client sign-off before implementation
```

## Link Naming Conventions

**Figma Design files** (`https://www.figma.com/design/...`):
```markdown
[Open Figma Design](https://www.figma.com/design/ABC123/...)
```

**FigJam boards** (`https://www.figma.com/board/...`):
```markdown
[Open FigJam Board](https://www.figma.com/board/ABC123/...)
```

## Sync Commands

Use the MCP tools or slash commands for syncing:

```bash
# Pull from ADO (overwrites local)
/ado-pull-items <id>
# Or use MCP tool: sync-work-item-to-file(project, [id])

# Push to ADO
/ado-push-items <id>
# Or use MCP tool: sync-work-item-from-file(project, [id])

# List local stories
# Use MCP tool: list-synced-work-items()
```

## Common Mistakes

- Missing `# Description` marker heading after frontmatter
- Using `#` instead of `##` for content sections (Client Requirement, Proposed Technical Approach, etc.)
- Adding blank lines between Given/When/Then lines
- Missing bold markers on Given/When/Then keywords
- Wrong AC header format (use `### AC1: Title` not `**AC1: Title**`)
- Putting TODO badge inline with heading instead of on the next line
- Treating template placeholders as client questions (they're for SI consultants)
- Removing Prerequisites section instead of keeping it with TODO badge
- **Writing ACs for OOTB functionality** (record creation, required field validation, form access)—only test custom behavior

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/klemensms) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
