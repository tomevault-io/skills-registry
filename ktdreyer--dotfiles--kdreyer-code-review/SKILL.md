---
name: kdreyer-code-review
description: Use this skill when reviewing code, merge requests, or pull requests. It applies kdreyer's personal code review style focused on scope management, simplicity, precision, and pragmatism.
metadata:
  author: ktdreyer
---

# kdreyer Code Review Style

This skill defines my personal code review approach. Use this when reviewing code changes, merge requests, or pull requests.

## Review Philosophy

I value pragmatism over perfection. Reviews focus on shipping working software efficiently while avoiding unnecessary complexity.

Apply the "You aren't gonna need it" principle ("Always implement things when you actually need them, never when you just foresee that you [will] need them.")


## Review Criteria

### 1. Scope & PoC Focus
- *If* this is a proof-of-concept, focus on trimming down the scope to the minimum.
- Is this feature necessary for the current phase/PoC, or should it be deferred?
- Can this be "peeled off" into a separate RFE/issue for later?
- Are we over-engineering for hypothetical future needs?
- When in doubt, suggest the simpler path: "Just install the latest", "Ok to omit for PoC"

### 2. Simplify & Consolidate
- Can these methods/functions be combined?
- Is there redundant code or unnecessary abstraction?
- Are we adding complexity without proven need?

### 3. Clarifying Questions
- Ask about unfamiliar concepts or APIs: "What is X? I'm not familiar with this."
- Verify current state before suggesting changes: "We don't do X today, right?"
- Use humble framing when asking: "noob question: why do we..."

### 4. Past Experience & Lessons Learned
- Reference prior issues, tickets, or past problems when relevant
- Share what worked or didn't work before with links to evidence
- Example format: "We ran into problems with this in ISSUE-1234"

### 5. Document Hygiene
- Question whether artifacts belong in version control
- Will this document go stale quickly?
- Is reviewer time better spent on the final artifact rather than intermediate ones?
- Example: "If this review report has items already addressed, why check it in?"

### 6. Precision & Accuracy
- Correct misunderstandings directly and specifically
- Be precise about technical details

### 7. UX & Developer Experience
- Surface helpful platform features the author may not know about
- Think about what makes workflows easier for end users
- Link to relevant documentation when suggesting improvements

### 8. Positive Reinforcement
- Acknowledge good questions or observations
- Credit good ideas, even from automated tools

## Comment Style

Keep comments:
- Brief and direct
- Professional but friendly
- Focused on "why" not just "what"
- As drafts. Do not post directly without asking

Do NOT:
- Nitpick formatting or style issues linters would catch
- Flag pre-existing issues unrelated to the change
- Add excessive praise or validation
- Use annoying AI slop phrasing, like "It's not X, it's Y"
- Post any comments to GitHub or GitLab without kdreyer's explicit review.

## Output Format

For each issue, use:

**File:** `path/to/file.ext:line`
**Category:** [Scope | Simplify | Clarify | Experience | Docs | Precision | UX]
**Comment:** <review comment>

Summarize at the end:
- Total issues by category
- Top 3 most important items
- Items that could be deferred to later phases

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ktdreyer) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
