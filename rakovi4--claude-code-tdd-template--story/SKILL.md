---
name: story
description: Generate story specifications from MVP stories list. Use when user wants to create detailed story documentation or mentions /story command. Use when this capability is needed.
metadata:
  author: rakovi4
---

# Generate Story Specification

Generate detailed story specifications following the established format, based on MVP stories list and product context.

## Usage
```
/story "Story name"
/story 5                    # By MVP story number
/story                      # Interactive selection
```

## Workflow

### Phase 1: Context Gathering

Before generating any specification, read and understand:

1. **Product description**: `ProductSpecification/BriefProductDescription.txt`
2. **MVP stories list**: `ProductSpecification/MvpStories.txt`
3. **Expected load**: `ProductSpecification/ExpectedLoad.txt`
4. **Archived drafts**: `ProductSpecification/Archived/DraftStories/1st-iteration/`
5. **Existing specifications**: `ProductSpecification/stories/*/NN_StoryName.md`
6. **Story-specific context** (optional): `ProductSpecification/stories/NN-story-name/story-specifics.txt`
   - Check if this file exists for the target story
   - If present, read it for additional context, external documentation, and special instructions
   - This handwritten file provides links to external APIs and integration requirements

### Phase 2: Story Selection

Parse user input to determine target story:

**By name:**
- `/story "Login/Logout"` — Match story name in MvpStories.txt
- `/story "Create Task"` — Partial match OK

**By number:**
- `/story 5` — Story #5 from MvpStories.txt

**Interactive:**
- `/story` — List available MVP stories, ask user to choose

Find related archived draft in `ProductSpecification/Archived/DraftStories/1st-iteration/` if exists. Use it as reference for the new specification.

### Phase 3: Generate Specifications

Generate **two files** - a compact main spec and a detailed notes file.

#### 3a. Main Story File: `NN_StoryName.md`

Brief, scannable, implementation-focused. **Target: ~50 lines max.**

```markdown
# [Story Title]

## Brief Description
[1-2 sentences max]

## Flow
[Numbered steps, max 10 steps]

## Acceptance Criteria
[Bullet points - what must work for story to be complete]

## Validation Rules
| Field | Rule |
|-------|------|
[Essential validations only - keep minimal]

## Screen States
[List of distinct screens/states needed for implementation]

## Core Requirements
[Critical implementation requirements - brief bullets, no fluff]
```

**Rules for main file:**
- No warnings, suggestions, or technical notes
- No "nice-to-haves" or future enhancements
- No verbose explanations - just facts
- Every line must be actionable for implementation

#### 3b. Notes File: `NN_StoryName_Notes.md`

All supplementary information goes here:

```markdown
# [Story Title] - Notes & Considerations

## Warnings

### Functional Warnings
[Things that could go wrong, edge cases]

### UI/UX Warnings
[UI/UX pitfalls to avoid]

### Technical Warnings
[Technical risks: security, performance, integration]

---

## Suggestions & Future Enhancements

### Functional Suggestions
[Enhancements, nice-to-haves]

### UI/UX Suggestions
[UI/UX improvements]

### Technical Suggestions
[Technical improvements and optimizations]

---

## Technical Notes

### Load Considerations
[Performance concerns based on ExpectedLoad.txt:
- 100 teams in 6 months
- Average task count per team won't exceed 500
- Max 5000 tasks per team
- Max 50 members per team]

### Security Considerations
[Security considerations - OWASP top 10, etc.]

### Infrastructure Notes
[Infrastructure/deployment concerns]

### Integration Notes
[Integration and external API concerns:
- External API dependencies
- OAuth token lifecycle
- Rate limits and throttling
- If story-specifics.txt exists, reference external API documentation]

---

## Additional Context

[If story-specifics.txt exists, reference it here:
- See `story-specifics.txt` for external API documentation
- External systems integrated (e.g., CalendarService API)
- OAuth flows, token types, API versions]
```

### Phase 4: Output

1. Determine story number from MvpStories.txt position
2. Convert story name to kebab-case for folder name (e.g., "Login/Logout" → "01-login-logout")
3. Create folder if needed: `ProductSpecification/stories/NN-story-name/`
4. Create **both files**:
   - Main spec: `NN_StoryName.md` (e.g., `01_LoginLogout.md`)
   - Notes file: `NN_StoryName_Notes.md` (e.g., `01_LoginLogout_Notes.md`)

**Story number mapping from MvpStories.txt:**
| # | Story |
|---|-------|
| 01 | Login/Logout |
| 02 | Registration |
| 03 | Password Reset |
| 04 | Connect Calendar |
| 05 | Create Task |
| 06 | Edit Task |
| 07 | Delete Task |
| 08 | Archive/Restore Task |
| 09 | Task detail view |
| 10 | Activity Log |
| 11 | Dashboard |
| 12 | Billing & Subscription |

### Phase 5: Summary

After creating specifications, provide:
1. **Main spec path** and line count (should be ~50 lines)
2. **Notes file path**
3. Brief confirmation that both files were created

## Example Invocations

```
/story "Create Task"
/story 1
/story "Login"
/story
```

## Design Constraints

- **Language**: English (documentation language per project rules)
- **Format**: Markdown with consistent heading hierarchy
- **Main file brevity**: Target ~50 lines max - ruthlessly cut fluff
- **Notes completeness**: All warnings, suggestions, and technical details go to Notes file
- **Load numbers**: Reference ExpectedLoad.txt metrics in Notes file only
- **Archived drafts**: Use as reference but apply new compact format
- **No redundancy**: If it's in main file, don't repeat in notes

## Notes

- Check if specification already exists before creating (avoid duplicates)
- If story already has specification, ask user if they want to regenerate
- Use archived drafts as starting point but apply new compact format
- Main file = implementation blueprint (what to build)
- Notes file = considerations (what to watch out for)
- Cross-reference related stories when relevant (e.g., Edit links to Create)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rakovi4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
