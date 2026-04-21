---
name: comment-reviewer
description: > Use when this capability is needed.
metadata:
  author: fangdav
---

# Comment Reviewer

Read comments and feedback left by others on a .docx file, then produce an actionable .md guide with editing advice and directions to take. The output lives in the topic's `Research/` folder so it's preserved alongside the source material.

## When to Use

- A .docx deliverable has received comments, tracked changes, or marginal notes from reviewers
- The user wants a clear summary of what feedback was given and what to do about it
- Before revising a document — to plan edits rather than reacting comment by comment

## Execution Flow

### Step 1: Identify the Document

Ask the user:
- Which .docx file has the comments?
- Who left the comments? (helps contextualize — e.g., feedback from legal vs. product has different weight)

Locate the .docx within its topic folder:
```
[Topic_of_Research]/
├── [Topic_of_Research].docx   ← This file
└── Research/
```

### Step 2: Extract All Comments

Read the .docx and extract every piece of feedback:
- **Margin comments** — Who wrote it, what section it's on, what it says
- **Tracked changes** — Insertions, deletions, formatting changes, who made them
- **Inline suggestions** — Text-level edits proposed by reviewers
- **General notes** — Any top-level or summary feedback (e.g., cover page notes, email context the user provides)

Present a raw list of all comments found and confirm with the user before proceeding.

### Step 3: Analyze and Categorize

Group feedback into categories:

| Category | Description |
|---|---|
| **Factual corrections** | Wrong data, outdated claims, incorrect attributions |
| **Structural feedback** | Reorder sections, add/remove sections, change flow |
| **Tone & style** | Writing is too formal/informal, unclear, verbose |
| **Missing content** | Reviewer wants something added that isn't there |
| **Challenges & pushback** | Reviewer disagrees with a finding or conclusion |
| **Questions** | Reviewer is asking for clarification, not suggesting a change |
| **Minor edits** | Typos, grammar, formatting — low effort fixes |

For each comment, assess:
- **Priority:** High (blocks approval), Medium (should address), Low (nice to have)
- **Effort:** Quick fix vs. requires new research vs. requires rethinking
- **Consensus:** Do multiple reviewers agree, or is this one person's opinion?

### Step 4: Generate the Editing Guide

Produce an .md file with this structure:

```markdown
# Comment Review: [Document Name]

**Author:** [Name]
**Date:** [YYYY-MM-DD]
**Document reviewed:** [Hyperlink to the .docx file]
**Reviewers:** [Names of people who left comments]

## Summary

[3-5 sentences. How many comments total? What's the overall sentiment — minor
tweaks needed, major rework, or fundamental disagreement? What are the biggest
themes?]

## High Priority — Must Address

### [Issue title]
- **Reviewer:** [Name]
- **Section:** [Which part of the doc]
- **Comment:** [What they said, quoted or paraphrased]
- **Recommended action:** [Specific advice on what to change and how]
- **Research needed:** [Yes/No — if yes, what to look into]

### [Next issue]
[Same structure]

## Medium Priority — Should Address

[Same structure per issue]

## Low Priority — Nice to Have

[Same structure per issue]

## Questions to Resolve

[Comments that are questions, not directives. For each:]
- **Reviewer:** [Name]
- **Question:** [What they asked]
- **Suggested answer/approach:** [How to address it]

## Conflicting Feedback

[Where two reviewers disagree or give contradictory advice:]
- **Reviewer A says:** [X]
- **Reviewer B says:** [Y]
- **Recommended resolution:** [Which direction to take and why]

## Editing Checklist

- [ ] [Specific edit 1 — section, what to change]
- [ ] [Specific edit 2]
- [ ] [Specific edit 3]
- [ ] ...
- [ ] Re-run through reviewers after edits

## References

[Vancouver style — if any recommended actions require new sources]
[1] Author. Title. Source. Year. URL.
```

### Step 5: File Placement

Place the editing guide inside the topic's `Research/` folder:

```
[Topic_of_Research]/
├── [Topic_of_Research].docx
└── Research/
    ├── README.md
    ├── [Existing research files...]
    └── [Document_Name]_Comment_Review.md   ← This file goes here
```

- Name: `[Document_Name]_Comment_Review.md`
- If multiple rounds of review happen, append the date: `[Document_Name]_Comment_Review_2026-02-17.md`
- Update the `Research/README.md` to reflect the new file
- Follow author sign-off and naming conventions from contribution guidelines

### Step 6: Hyperlinks

1. **Link to the .docx** — The editing guide must hyperlink to the document it's reviewing (relative path up to the topic folder)
2. **Link to existing research** — If a recommended action references an existing .md file in `Research/`, hyperlink to it
3. **Link from .docx context** — If the user plans to revise, they can reference this editing guide from within the document

## Style

- **Actionable.** Every piece of feedback should end with a clear recommended action — not just "reviewer said X" but "do Y in response"
- **Prioritized.** High-priority items first. The user should be able to read just the top section and know what matters most.
- **Diplomatic.** Summarize feedback neutrally. Don't editorialize on whether a reviewer is right or wrong — present the feedback and recommend a path forward.
- **Specific.** Reference exact sections of the document. "Update the market size figure in Section 2.3" not "fix the numbers."

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fangdav) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
