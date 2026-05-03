---
name: meeting-processor
description: Process biweekly meeting transcripts into GitHub issues Use when this capability is needed.
metadata:
  author: mehtan-rahman
---

Process the meeting transcript and create GitHub issues following these rules:

## Transcript Analysis

Read the transcript from: $ARGUMENTS

Extract:
1. Action items mentioned
2. New experiments or comparison runs proposed
3. Library features, bugfixes, or refactors needed
4. Analysis tasks
5. Documentation, writing, or proposal tasks
6. Infrastructure or process setup
7. Follow-ups with people, recurring meetings, collaboration
8. Reading/study tasks (papers, chapters, learning goals)
9. Decisions made
10. Topics needing more discussion or design

## Issue Categorization

Create issues with these prefixes:
- `[EXPERIMENT]` - New experiments, parameter sweeps, or comparison runs (e.g. run optimizer on different systems, test ensemble sizes, compare timeout vs no timeout)
- `[ANALYSIS]` - Data analysis tasks
- `[LIBRARY]` - Code changes: features, bugfixes, refactors (e.g. log2 step/decode, new options, dtype fixes, bounds, warnings, stop conditions, ownership)
- `[DOCUMENTATION]` - Paper writing, documentation updates, plain-language guides, proposal edits
- `[INFRASTRUCTURE]` - Setup, CI/CD, tooling, recurring process setup (e.g. weekly meetings)
- `[DISCUSSION]` - Topics needing more discussion or design before implementation (e.g. rethink approach)
- `[FOLLOW-UP]` - People/meetings: follow up with X, attend recurring meetings, collaborate with someone
- `[STUDY]` - Reading and learning: papers, chapters, concepts (e.g. read paper, study chapter, learn topic with collaborator)

## Issue Structure

For each issue:
```markdown
Title: [CATEGORY] Brief description

## Context
[What was discussed in the meeting]

## Acceptance Criteria
- [ ] Specific deliverable 1
- [ ] Specific deliverable 2
- [ ] Specific deliverable 3

## Technical Notes
[Any specific approaches mentioned]

## Dependencies
[Links to related issues or library changes]

## Meeting Reference
Date: [meeting date]
Participants: [names]
Transcript: meetings/transcripts/YYYY-MM-DD-meeting.txt
```

## Priority Labels

Assign labels based on urgency mentioned:
- `priority:high` - Blocking progress or deadline-driven
- `priority:medium` - Important but not blocking
- `priority:low` - Nice to have

Additional labels:
- `needs-discussion` - If approach unclear
- `library-change` - Requires library modification
- `paper-related` - Related to manuscript

## For Library Issues

When creating a `[LIBRARY]` issue:
- Create it in the PROJECT repo (not library repo)
- Add clear note: "Implements in library repo at ../../libraries/[name]"
- Add note: "Validate in this project after implementation"
- Link to relevant experiments that need this feature

## Summary Generation

After creating issues, generate:
```markdown
# Meeting Summary: YYYY-MM-DD

## Attendees
[List participants]

## Key Decisions
[Major decisions made]

## Issues Created
[Table of created issues with links and priorities]

| Issue | Category | Priority | Description |
|-------|----------|----------|-------------|
| #[num] | [CATEGORY] | [priority] | [brief desc] |

## Next Meeting
Date: [Next meeting date]
Focus: [What to prepare]

## Action Items Summary
- [ ] Issue #X - [Brief]
- [ ] Issue #Y - [Brief]
```

Save summary to: `meetings/summaries/YYYY-MM-DD-summary.md`

## Execution

1. Read transcript thoroughly
2. Identify all action items and discussions
3. Create GitHub issues using `gh issue create`
4. Apply appropriate labels
5. Generate summary markdown
6. Link everything together

## Quality Checks
- [ ] No duplicate issues
- [ ] All action items captured
- [ ] Priorities make sense
- [ ] Acceptance criteria are testable
- [ ] Dependencies are noted
- [ ] Summary is comprehensive

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mehtan-rahman) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
