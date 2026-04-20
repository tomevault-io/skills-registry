---
name: handoff
description: Create a detailed handoff document for continuing work in a new session. Use when the user says handoff, wants to save their progress, needs to continue later, or is ending a session with unfinished work. Writes a comprehensive summary to .claude/handoffs/ for pickup by a future agent. Use when this capability is needed.
metadata:
  author: jacobh
---

# Handoff

Creates a detailed handoff plan of the conversation for continuing the work in a new session.

The user specified purpose:

<purpose>$ARGUMENTS</purpose>

You are creating a summary specifically so that it can be continued by another agent. For this to work you MUST have a purpose. If no specified purpose was provided in the `<purpose>...</purpose>` tag you must STOP IMMEDIATELY and ask the user what the purpose is.

Do not continue before asking for the purpose as you will otherwise not understand the instructions and do not assume a purpose!

## Goal

Your task is to create a detailed summary of the conversation so far, paying close attention to the user's explicit purpose for the next steps.
This handoff plan should be thorough in capturing technical details, code patterns, and architectural decisions that will be essential for continuing development work without losing context.

## Process

Analyze the conversation internally (do not output analysis to chat). In your analysis:

1. Chronologically analyze each message and section of the conversation. For each section thoroughly identify:
   - The user's explicit requests and intents
   - Your approach to addressing the user's requests
   - Key decisions, technical concepts and code patterns
   - Specific details like file names, full code snippets, function signatures, file edits, etc
2. Double-check for technical accuracy and completeness, addressing each required element thoroughly.

Your plan should include the following sections:

1. **Primary Request and Intent**: Capture all of the user's explicit requests and intents in detail
2. **Key Technical Concepts**: List all important technical concepts, technologies, and frameworks discussed.
3. **Files and Code Sections**: Enumerate specific files and code sections examined, modified, or created. Pay special attention to the most recent messages and include full code snippets where applicable and include a summary of why this file read or edit is important.
4. **Problem Solving**: Document problems solved and any ongoing troubleshooting efforts.
5. **Pending Tasks**: Outline any pending tasks that you have explicitly been asked to work on.
6. **Current Work**: Describe in detail precisely what was being worked on immediately before this handoff request, paying special attention to the most recent messages from both user and assistant. Include file names and code snippets where applicable.
7. **Optional Next Step**: List the next step that you will take that is related to the most recent work you were doing. IMPORTANT: ensure that this step is DIRECTLY in line with the user's explicit requests, and the task you were working on immediately before this handoff request. If your last task was concluded, then only list next steps if they are explicitly in line with the users request. Do not start on tangential requests without confirming with the user first.
8. **Bootstrap Context**: Provide context to help the next agent get up to speed quickly:
   - **Files to Read**: List the most important files (with absolute paths) that the next agent should read first, with a brief reason for each
   - **Suggested Exploration** (optional): If helpful, suggest specific searches or areas to explore (e.g., "Search for `FunctionName` in `src/`")

Additionally create a "slug" for this handoff. The "slug" is how we will refer to it later in a few places. Examples:

* current-user-api-handler
* implement-auth
* fix-issue-42

Together with the slug create a "Readable Summary". Examples:

* Implement Current User API Handler
* Implement Authentication
* Fix Issue #42

## Quality Guidelines

These guidelines are derived from analysis of 67+ real handoffs. Follow them to produce handoffs that the picking-up agent can act on immediately.

### Calibrate Verbosity to Task Complexity

Not every handoff needs 200+ lines. Match depth to the task:

| Task complexity | Handoff size | Example |
|----------------|-------------|---------|
| One-line fix (remove a filter, change a constant) | ~50 lines — collapse §2-4 | "Remove filter on line 681 of sync-service.ts" |
| Targeted change (add a method, modify a type) | ~100-150 lines | Standard template |
| Multi-file refactor or new feature | ~150-250 lines | Full template with code snippets |
| Design decision or investigation | ~150-200 lines | Emphasis on §4 and §7 |

### Code Snippets: Be Surgical

Code snippets are the most valuable part of a handoff — when used well. Follow these rules:

- **DO include**: Interfaces/types to implement against, patterns to follow ("follow the existing `isMergeInProgress` implementation at lines 343-350"), exact code to write in §7
- **DO include**: Before/after comparisons, field mapping tables, comparison tables
- **DON'T include**: Full interface definitions when only 2-3 fields are relevant — use `// ... other fields omitted` with a file+line reference
- **DON'T include**: Full function bodies for code being *deleted* — a line range reference is sufficient
- **DON'T include**: Code already visible by reading the file — reference the file+line instead

**Best pattern**: Quote the *relevant excerpt* and point to the file for full context:
```typescript
// From src/services/sync-service.ts lines 79-82
interface ISyncService {
  getUnsyncedPRs(): Promise<UnsyncedPR[]>;  // <-- add this method
  // ... 15 other methods omitted
}
```

### Verify Before Handing Off

**State facts, not guesses.** Never write "likely" or "probably" — read the file and confirm.

- ❌ "The state machine **likely** still has `parsedUrl: GitHubPRRef`"
- ✅ "The state machine at line 45 of `CherryPickWizardScreen.tsx` uses `parsedUrl: GitHubPRRef` (singular)"

**Don't hand off prematurely.** If the next step is "try running the command again," do that first. Only hand off when there's meaningful work for the next agent.

### Section-Specific Guidance

**§4 Problem Solving** — When there are no problems, document **assumptions and risks** instead:
- ❌ "No problems encountered."
- ✅ "No problems encountered. Key assumptions: (1) CHERRY_PICK_HEAD behaves like MERGE_HEAD for progress detection, (2) MockGitClient needs no special cherry-pick state tracking."

**§5 vs §7 — Distinguish clearly:**
- §5 (Pending Tasks) = all remaining work *after* the handoff is completed
- §7 (Next Step) = the *immediate first action* for the picking-up agent
- When they're the same (single-task handoff), §5 should say "See Next Step"

**§7 Next Step — Make recommendations, don't defer decisions:**
- ❌ "Analyze the difference between X and Y to determine which approach to use"
- ✅ "Recommended approach: Option B — have the wizard stage files before calling `checkAndCommitIfResolved`. Rationale: ..."
- If you genuinely can't decide, flag it: "⚠️ Design decision needed — ask the user before proceeding"

**§7 for investigations — Rank hypotheses by likelihood:**
- ❌ Four investigation paths listed without prioritization
- ✅ "Most likely cause (80%): shared terminal state between tests. Start by checking test isolation. Fallback: OpenTUI scrollbar rendering logic."

**§7 for open questions — Suggest a default:**
- ❌ "Question: Should cherry-pick PRs be recorded in the sync database?"
- ✅ "Question: Should cherry-pick PRs be recorded in the sync database? Default to: yes, record them, since they need to appear in the unsynced count. Confirm with the user."

### Bootstrap Context (Section 8) — Required

This section is **mandatory**. It's the single highest-leverage section for pickup-ability.

**Files to Read rules:**
- Use **absolute paths** — the picking-up agent shouldn't have to guess
- **Validate paths exist** before writing them (wrong paths waste pickup time)
- Include **line numbers or function names** when pointing to specific code: "`convertToUpstreamPR` at ~line 250"
- Order by importance — most critical file first
- 3-6 files is the sweet spot; more than 8 means you're listing too many
- Always include relevant **test files** — the picking-up agent needs to know testing patterns

**Suggested Exploration rules:**
- Use project-appropriate tools (e.g., `rg` not `grep` if the project requires it)
- Be specific: `rg "cherryPick" src/services/` not "search for cherry-pick usage"

### Related Handoffs

When this handoff is part of a sequence or depends on prior work, note it:
- "Follows: `2026-01-13-impl-get-unsynced-prs.md`"
- "Prerequisite: The SyncService TTL logic from `2026-01-21-sync-service-ttl.md` must be completed first"

### Slug Naming

Good slugs are:
- **Kebab-case** — no camelCase or PascalCase embedded (`sync-db-upstream-pr-pipeline` not `sync-db-UpstreamPR-pipeline`)
- **Under 40 characters** — `replace-pr-types-cherry-pick` not `replace-pullrequest-with-upstreampr-in-cherry-pick-wizard`
- **Action-oriented** — use verbs: `impl-`, `fix-`, `debug-`, `refactor-`, `add-`
- **Domain-specific** — describe what changed, not internal milestones (`sync-db-upstream-pr-pipeline` not `sync-db-step-2`)
- **Distinct** — avoid near-duplicate names on the same date
- **Consistent verb style** — pick `impl-` or `implement-`, don't alternate

### Avoid Repeating Shared Context

When multiple handoffs exist for the same feature, don't repeat the full feature description each time. Reference a canonical source:
- ✅ "See `docs/sync-database-design.md` §4 for the full design"
- ❌ (Pasting the same 15-line feature requirements block in every handoff)

## Output

**IMPORTANT: Do NOT output the full handoff plan to the chat.** Write directly to the file.

1. Do your analysis internally (use thinking/reasoning, not chat output)
2. Write the handoff document directly to `.claude/handoffs/[timestamp]-[slug].md` where `[timestamp]` is the current date in format `YYYY-MM-DD`
3. After writing the file, provide only a brief summary to the user (2-3 sentences max) confirming the handoff was created

### Handoff File Structure

The markdown file you write should follow this structure:

```markdown
# [Readable Summary]

## 1. Primary Request and Intent
[Detailed description of all user requests and intents]

## 2. Key Technical Concepts
- [Concept 1]
- [Concept 2]
- [...]

## 3. Files and Code Sections
### [File Name 1]
- **Why important**: [Summary of why this file is important]
- **Changes made**: [Summary of the changes made to this file, if any]
- **Code snippet**:
\`\`\`language
[Important Code Snippet — only the relevant excerpt, not the full file]
\`\`\`

### [File Name 2]
...

## 4. Problem Solving
[Description of solved problems and ongoing troubleshooting. If no problems, document assumptions and risks instead.]

## 5. Pending Tasks
[Remaining work after the immediate next step is completed. If this is a single-task handoff, say "See Next Step."]

## 6. Current Work
[What was being worked on immediately before handoff]

## 7. Next Step
[Required next step, directly aligned with user's explicit handoff purpose. Include recommendations, not deferred decisions. For investigations, rank hypotheses by likelihood.]

## 8. Bootstrap Context

**Related handoffs**: [Any prerequisite or follow-up handoffs, if applicable]

### Files to Read
[3-6 most important files with absolute paths and brief reasons. Include test files.]
- `/absolute/path/to/file.ts` (lines 79-115) - [why this file matters]

### Suggested Exploration
[Specific searches and verification commands]
- `rg "PatternName" src/services/`
```

### User Confirmation

After writing the file, tell the user:
- The filename that was created
- That they can use `/pickup FILENAME` to continue

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jacobh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
