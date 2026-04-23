---
name: retrospective
description: Context engineering audit after mistakes. Use when user says "retrospective", "retro", "introspect", "context audit", "what went wrong", or asks to analyze/prevent session mistakes. Invoke IMMEDIATELY when these triggers appear - don't do ad-hoc analysis. Use when this capability is needed.
metadata:
  author: artymclabin
---

# Retrospective

YOUR ENTIRE VISIBLE OUTPUT MUST BE ONE OF THESE THREE TEMPLATES. NOTHING ELSE.

## TEMPLATE A — Gaps found
```
**BLUF: All addressed in-session (X/Y).** ← or "X/Y addressed in-session." if partial

**Unaddressed:**
1. Add "X rule" → file.md (gap: did Y instead of Z)

**Addressed:**
1. Add "X rule" → file.md (gap: did Y instead of Z) ✅ applied

**Session:** One sentence.
**Closure: Safe to close.** ← or "NOT safe." with numbered list of unfinished items
```

If all gaps are addressed, omit the Unaddressed section. If none are addressed, omit the Addressed section.

## TEMPLATE B — No gaps, non-trivial session
```
**No gaps.**

**Session:** One sentence.
**Closure: Safe to close.** ← or "NOT safe." with numbered list of unfinished items
```

## TEMPLATE C — No gaps, trivial session
```
**No gaps.**
```

NO TEXT BEFORE THE TEMPLATE. NO ANALYSIS. NO EXPLANATION.

## Scope Rule

**Two sections, both mandatory:**

### Section 1: Context Engineering Gaps
Rules missing, rules not followed, knowledge to persist. This is the gap analysis.

### Section 2: Session Closure Readiness
**Can user close this session without losing work?** Check for:
- Uncommitted code changes (`git status`)
- Unpushed commits
- Unsubmitted QA (changes deployed but no QA message sent)
- Unfinished work promised this session (e.g., "I'll write that test" but didn't)
- Pending builds/deploys that haven't been verified

If ALL clear → append `**Closure: Safe to close.**` after the Session line.
If NOT clear → append `**Closure: NOT safe.** Unfinished:` with a numbered list.

**This prevents the false "all done" signal** that caused the user to almost close a session with 5 unfinished items (Mar 2026 incident).

## Post-Template Behavior

**Default: Auto-address immediately.**

After outputting the template:
1. **If Unaddressed items exist** → immediately proceed to fix them (edits, updates, etc.)
2. **After each edit, VERIFY it landed** → grep/read the file for the new content. If not found, the edit failed — retry. Never report "addressed" without verification evidence.
3. **If an item needs user input** → ask only for that item, continue with others
4. **Report results** after all auto-addressable items are done, using this format:
   `"X/Y addressed (found + finalized N gap(s)). All done."` — where X/Y is the final tally and N is how many gaps were discovered and fixed during the auto-address phase (gaps that weren't already addressed in-session). If no new gaps were found during auto-address, just `"X/Y addressed. All done."`

**Exception — Pause mode:**
If user explicitly says "retrospective and pause", "retro only", "just report", or similar → output template only, don't auto-address. Wait for user to say "address it" or similar.

## Anti-Confabulation Rule

🚨 **NEVER fabricate, misquote, or reconstruct prior outputs from memory.** When referencing what you previously said (counts, quotes, claims), either:
1. Cite it exactly with the message context, or
2. Say "I don't have the exact text" and describe the gist

**Incident (Mar 2026):** In a meta-retro, Claude claimed "I said 4/4" when the actual output was "3/4 addressed." Under pressure of being challenged, fabricated a worse version of its own error to construct a coherent narrative. This is confabulation — inventing false details when uncertain. It destroys trust faster than the original mistake.

**Rule:** If challenged on what you previously reported, don't guess. Say what you know and what you don't.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/artymclabin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
