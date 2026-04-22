---
name: prose
description: Apply Strunk's writing rules to prose—documentation, commits, error messages, UI text. Clearer, stronger, more professional. Also handles sharpening and tightening drafts by subtraction. Use when this capability is needed.
metadata:
  author: tslateman
---

# Writing Clearly and Concisely

## Overview

William Strunk Jr.'s _The Elements of Style_ (1918) teaches you to write clearly and cut ruthlessly.

**WARNING:** `references/elements-of-style.md` consumes ~12,000 tokens. Read it only when writing or editing prose.

## When to Use This Skill

Use this skill whenever you write prose for humans:

- Documentation, README files, technical explanations
- Commit messages, pull request descriptions
- Error messages, UI copy, help text, comments
- Reports, summaries, or any explanation
- Editing to improve clarity
- Tightening or sharpening a draft that runs long

**If you're writing sentences for a human to read, use this skill.**

## Limited Context Strategy

When context is tight:

1. Write your draft using judgment
2. Dispatch a subagent with your draft and `references/elements-of-style.md`
3. Have the subagent copyedit and return the revision

## All Rules

### Elementary Rules of Usage (Grammar/Punctuation)

1. Form possessive singular by adding 's
2. Use comma after each term in series except last
3. Enclose parenthetic expressions between commas
4. Comma before conjunction introducing co-ordinate clause
5. Don't join independent clauses by comma
6. Don't break sentences in two
7. Participial phrase at beginning refers to grammatical subject

### Elementary Principles of Composition

8. One paragraph per topic
9. Begin paragraph with topic sentence
10. **Use active voice**
11. **Put statements in positive form**
12. **Use definite, specific, concrete language**
13. **Omit needless words**
14. Avoid succession of loose sentences
15. Express co-ordinate ideas in similar form
16. **Keep related words together**
17. Keep to one tense in summaries
18. **Place emphatic words at end of sentence**

### Section V: Words and Expressions Commonly Misused

Alphabetical reference for usage questions

## Editing by Subtraction

Sharpening a draft means removing everything that does not serve the core point. Not rewriting. Not adding "voice." Cutting.

### Core Principles

**Cut Structure, Keep Substance.** Drafts bloat through structure, not through ideas. Headers, transitions, framing paragraphs, and setup sentences add length without adding meaning. Cut them first. The substance survives.

**Catch LLM-isms.** AI-generated text carries recognizable signatures: contrastive pivots, filler authority phrases, uniform tone, and predictable word choices. These patterns weaken prose even when the underlying argument is strong. Identify them and cut or rewrite.

**Substance Over Voice for High-Stakes Prose.** "Humanizing" prompts add colloquialisms and personality but dilute the core argument. For leadership pitches, technical proposals, and executive communication, fluff hurts more than polish helps. Prioritize the argument.

**Trust the Author's Cuts.** A single note from the author ("cut 'The stakes are real'") often does more than an elaborate rewriting pass. The author knows what matters. When they cut, follow the instinct rather than restoring through rewording.

### LLM-ism Catalog

Recognizable AI writing patterns and their fixes:

| Pattern                     | Example                                         | Fix                                             |
| --------------------------- | ----------------------------------------------- | ----------------------------------------------- |
| Contrastive pivot           | "That's not a trend piece -- that's the stakes" | State what it is. Skip the negation setup.      |
| Needless stakes declaration | "The stakes are real."                          | Delete it. If the argument is strong, it shows. |
| Uniform sentence rhythm     | Every paragraph same length, same cadence       | Vary length. Let some sentences punch.          |
| Hedged authority            | "It's worth noting that..."                     | Drop the hedge. State the point.                |
| Formulaic structure         | Intro paragraph, three body sections, summary   | Start with the strongest point. Cut the frame.  |
| Predictable word choices    | Always the most probable next word              | Choose the specific word, not the expected one. |
| Throat-clearing openers     | "When we think about X, we need to consider..." | Delete and start at the verb.                   |

### Sharpening Workflow

1. **Read the draft.** Identify the core point in one sentence
2. **Cut everything that does not serve it.** Framing, transitions, restatements, setup paragraphs
3. **Scan for LLM patterns.** Check the catalog above. Fix or delete each instance
4. **Verify nothing was lost.** Read the shortened version. Does every important idea survive? If yes, the cut version is the better version

### The Test

Can you remove this sentence without losing meaning? Remove it.

## Bottom Line

Writing for humans? Read `references/elements-of-style.md` and apply the rules. Low on tokens? Dispatch a subagent to copyedit with the guide.

## See Also

- `/naming` — Strunk's Rule 12 applies identically to code names
- `/review` — Write review comments clearly using these rules
- `skills/FRAMEWORKS.md` — Full framework index
- `RECIPE.md` — Agent recipe for parallel sharpening (3 workers)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tslateman) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
