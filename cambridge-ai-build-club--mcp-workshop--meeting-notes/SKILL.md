---
name: meeting-notes
description: Creates structured meeting notes, minutes, and action items from conversations or transcripts. Use when documenting meetings, standups, retrospectives, or extracting action items from discussions.
metadata:
  author: cambridge-ai-build-club
---

# Meeting Notes

## Quick Start

For any meeting, capture:
1. **Context**: Date, attendees, purpose
2. **Key Points**: Main discussion topics
3. **Decisions**: What was decided
4. **Actions**: Who does what by when

## Meeting Type Workflows

**Standup/Daily?** → Quick format, focus on blockers
**Team Meeting?** → Standard format with discussion notes  
**Board/Formal?** → Detailed minutes with motions
**Retrospective?** → What worked, what didn't, improvements

For meeting templates, see [TEMPLATES.md](TEMPLATES.md).

## From Transcript to Notes

When processing a transcript:

```
Processing Checklist:
- [ ] Identify meeting type and attendees
- [ ] Extract key discussion points
- [ ] Note all decisions made
- [ ] List action items with owners and deadlines
- [ ] Summarize in appropriate template
```

**Step 1:** Scan for meeting context (who, what, when)

**Step 2:** Identify decision points (look for: "let's go with", "agreed", "we'll do")

**Step 3:** Extract action items (look for: "will", "by Friday", "take the lead on")

**Step 4:** Apply appropriate template from TEMPLATES.md

## Action Item Format

Always structure action items as:

```
[ ] [OWNER]: [Task description] — [Deadline]
```

Example:
```
[ ] @alice: Update API documentation — Dec 15
[ ] @bob: Review security findings — EOD Friday
[ ] @team: Submit feedback on proposal — Next standup
```

## Output Quality

Good meeting notes are:
- **Scannable**: Key info visible at a glance
- **Actionable**: Clear next steps with owners
- **Complete**: Nothing important omitted
- **Concise**: No unnecessary detail

For output examples, see [EXAMPLES.md](EXAMPLES.md).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cambridge-ai-build-club) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
