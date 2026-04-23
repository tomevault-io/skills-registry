---
name: research
description: Diagnose research quality and guide systematic query expansion. Use when starting research on any topic, when stuck in research, or when unsure if research is complete. Use when this capability is needed.
metadata:
  author: jwynia
---

# Research Skill

Systematic research query expansion and completion assessment. Transforms basic questions into comprehensive search strategies.

## Diagnostic States

### R1: No Analysis
**Symptoms:** Jumping straight to searching without analyzing the topic.
**Test:** Can you articulate stakeholders, temporal scope, and domain mapping?
**Intervention:** Run Phase 0 Analysis Template before generating queries.

### R1.5: No Vocabulary Map
**Symptoms:** Using outsider/introductory terminology. Finding only surface-level material.
**Test:** Have you identified expert vs. outsider terms? Terms across domains?
**Intervention:** Build vocabulary map. Hunt for "also known as," "technically called" in early sources.

### R2: Single-Perspective Search
**Symptoms:** All queries support one viewpoint. Missing counterarguments.
**Test:** Have you explicitly searched for opposing perspectives?
**Intervention:** Generate competing perspectives queries. Search for strongest counterargument.

### R3: Domain Blindness
**Symptoms:** Searching only in familiar field. Missing cross-disciplinary insights.
**Test:** Have you mapped terminology variants across fields?
**Intervention:** Identify what adjacent fields call this topic. Search in at least 2 domains. Update vocabulary map.

### R4: Recency Bias
**Symptoms:** Only recent sources. Missing historical context.
**Test:** Can you explain when this topic emerged and how it evolved?
**Intervention:** Add historical context queries. Find seminal works.

### R5: Breadth Without Depth
**Symptoms:** Many tabs, no synthesis. Can't explain core concepts.
**Test:** Can you define key terms in your own words?
**Intervention:** Apply 3-source rule per perspective. Summarize before searching more.

### R6: Completion Uncertainty
**Symptoms:** Unsure whether to continue or stop. Research expanding indefinitely.
**Test:** Can you answer the tiered completion criteria?
**Intervention:** Run completion checklist. Look for diminishing returns signals.

### R7: Research Complete
**Symptoms:** Can explain topic, identify uncertainties, and take action.
**Indicators:** Circular references, repetitive findings, sufficient for purpose.

### R8: No Persistence
**Symptoms:** Starting from scratch each session. Re-discovering same vocabulary.
**Test:** Did you check for prior research before starting? Are you storing findings?
**Intervention:** Store vocabulary map, sources, digested notes, and gaps for future use.

### R9: Scope Mismatch
**Symptoms:** Over-researching trivial questions. Under-researching critical decisions.
**Test:** Is research depth proportional to decision stakes?
**Intervention:** Apply scope calibration. Match confidence level to decision reversibility and stakes.

### R10: No Confidence Signaling
**Symptoms:** Hedging language everywhere. Reader can't tell what's certain vs. speculative.
**Test:** Can reader distinguish established facts from speculation?
**Intervention:** Use explicit confidence markers. State source quality and consensus status.

## Phase 0: Analysis Template

Before searching, structure your topic:

```markdown
# Research Analysis: [Topic]

## Core Concepts
- **Primary terms:** [Key terms requiring definition]
- **Terminology variants:** [Synonyms, jargon, historical terms]
- **Ambiguous terms:** [Terms with multiple meanings]

## Stakeholders
- **Primary actors:** [Who is directly involved?]
- **Affected groups:** [Who bears consequences?]
- **Opposing interests:** [Who benefits from different outcomes?]

## Temporal Scope
- **Historical origins:** [When did this begin?]
- **Key transitions:** [What changed and when?]
- **Current state:** [What's happening now?]

## Domains
- **Primary field:** [Main discipline]
- **Adjacent fields:** [Related disciplines]

## Controversies
- **Active debates:** [What's contested?]
- **Competing frameworks:** [Different ways of understanding]
```

## Query Types

1. **Foundational:** "term definition AND field review"
2. **Historical:** "topic history development [date range]"
3. **Current:** "topic current trends [recent years]"
4. **Competing:** "topic debate AND (perspective1 OR perspective2)"
5. **Evidence:** "topic impact measurement study data"

## Completion Criteria

### Minimum Viable (Quick Decisions)
- [ ] Can define core concepts in own words
- [ ] Know 2-3 major perspectives
- [ ] Found authoritative source per perspective
- [ ] Identified known unknowns

### Working Knowledge (Most Decisions)
- [ ] Can explain historical context
- [ ] Understand stakeholder positions
- [ ] Encountered counterarguments
- [ ] Checked multiple domains

### Deep Expertise (High-Stakes)
- [ ] Traced claims to primary sources
- [ ] Can evaluate competing evidence
- [ ] Understand knowledge limitations

## Diminishing Returns Signals

Stop when:
- New sources cite same foundational works (circular)
- New searches return familiar content (repetitive)
- Each hour adds less than previous (marginal)
- Can make decision or take action (sufficient)

## Anti-Patterns

| Pattern | Symptom | Fix |
|---------|---------|-----|
| Confirmation Trap | Searching to confirm, not learn | Search for strongest counterargument |
| Authority Fallacy | Accepting claims by source prestige | Evaluate evidence, not source |
| Recency Trap | Only recent sources | Explicitly search historical periods |
| Breadth Trap | 50 tabs, none read | 3-source rule, summarize before continuing |
| Single-Source | Wikipedia as final answer | Require 3 independent sources |
| Jargon Blind Spot | Missing other fields' terminology | Map variants, search multiple domains |
| Infinite Rabbit Hole | Lost original purpose | Write decision/action anchor, return to it |

## Vocabulary Mapping

**Primary research deliverable.** Vocabulary determines search space and LLM semantic activation.

### Why It Matters
- Expert terms → expert material. Outsider terms → introductory material.
- Precise vocabulary activates richer LLM semantic space.
- Cross-domain terms bridge bodies of work that use different names.

### Vocabulary Map Template
```markdown
## Core Terms
| Term | Domain | Depth Level |
|------|--------|-------------|
| [expert term] | [field] | Expert |
| [outsider term] | General | Introductory |

## Cross-Domain Synonyms
| Concept | Terms by Domain |
|---------|-----------------|
| [concept] | Field A: [term], Field B: [term] |

## Depth Indicators
| Level | Terms | What They Surface |
|-------|-------|-------------------|
| Introductory | [terms] | Overviews, explainers |
| Expert | [terms] | Research, nuanced analysis |
```

### Discovery Process
1. Note which terms feel like outsider language
2. In early sources, watch for "also known as," "technically called"
3. Map terms across domains
4. Test different terms in searches, note what surfaces

## Research Persistence

**Store both sources AND digested results.** Don't start from scratch.

### What to Store
| Layer | Contents |
|-------|----------|
| Vocabulary Map | Terms, domains, depth levels |
| Sources | PDFs, saved pages, bookmarks |
| Digested Notes | Summaries, key quotes, synthesis |
| Query Log | Searches that worked/failed |
| Gaps | What remains unknown |

### Before Starting
Check for prior research. Load vocabulary map. Start where you left off.

## Single-Shot Research

When research runs without follow-up questions (agent execution, time-boxed queries):

### Scope Calibration

| Decision Type | Confidence Needed | Research Depth |
|---------------|-------------------|----------------|
| Reversible, low-stakes | 60-70% | Quick scan (minutes) |
| Reversible, moderate | 75-85% | Working knowledge (1-2 hours) |
| Irreversible, moderate | 85-90% | Solid grounding (half day) |
| Irreversible, high | 90-95% | Deep expertise (days) |

### Question Pattern → Strategy

| Pattern | Strategy |
|---------|----------|
| "What is X?" | 2-3 authoritative sources, establish consensus |
| "Should I X?" | Pros/cons, alternatives, conditions for each |
| "Is X true?" | Primary sources, counter-evidence, consensus check |
| "How do I X?" | Step-by-step, prerequisites, common pitfalls |

### Source Type Selection

| Source | Best For |
|--------|----------|
| Wikipedia/Encyclopedias | Orientation, terminology, citation hunting |
| Academic papers | Mechanism, causation, methodology |
| Practitioner content | How things actually work, edge cases |
| Official docs | Technical specs, policy, procedures |

### Synthesis Template

```markdown
## Summary
[Direct answer to question]

## Confidence Level
[High/Medium/Low] - [Justification]

## Key Findings
1. [Finding with source type]

## Caveats
- [What wasn't consulted]
- [What assumptions were made]

## For Deeper Investigation
[What would increase confidence]
```

### Confidence Markers

| Level | Phrases |
|-------|---------|
| Established | "X is...", "X works by..." |
| Strong evidence | "Evidence strongly suggests..." |
| Moderate evidence | "Most sources report..." |
| Limited evidence | "One study found..." |
| Unknown | "No reliable information found..." |

### Single-Shot Checklist

- [ ] Scope matched to stakes?
- [ ] Multiple source types consulted?
- [ ] Counter-evidence sought?
- [ ] Confidence level explicit?
- [ ] Gaps acknowledged?

## Health Check Questions

During research, ask:
1. Am I searching to learn or to confirm?
2. What's the strongest argument against my current view?
3. Have I looked outside my familiar domains?
4. Can I summarize what I've learned so far?
5. Is this still serving my original purpose?
6. Am I using expert or outsider vocabulary?
7. Have I stored what I've learned for future use?
8. Is my depth proportional to the stakes?
9. Am I signaling confidence explicitly?

## Integration Points

| Skill | Connection |
|-------|------------|
| **doppelganger** | Research informs decisions; apply /truth-check to findings |
| **context-networks** | Store research findings in appropriate network node |
| **boundary-critique** | Apply to advice and recommendations encountered |

## Output Persistence

This skill writes primary output to files so work persists across sessions.

### Output Discovery

**Before doing any other work:**

1. Check for `context/output-config.md` in the project
2. If found, look for this skill's entry
3. If not found or no entry for this skill, **ask the user first**:
   - "Where should I save output from this research session?"
   - Suggest: `explorations/research/` or a sensible location for this project
4. Store the user's preference:
   - In `context/output-config.md` if context network exists
   - In `.research-output.md` at project root otherwise

### Primary Output

For this skill, persist:
- **Vocabulary map** - terms, domains, depth levels, cross-domain synonyms
- **Phase 0 analysis** - core concepts, stakeholders, temporal scope, domains
- **Synthesis document** - summary, confidence levels, key findings, caveats
- **Source assessment** - sources consulted with quality notes
- **Gaps identified** - what remains unknown, next steps

### Conversation vs. File

| Goes to File | Stays in Conversation |
|--------------|----------------------|
| Vocabulary map | Discussion of term discovery |
| Synthesis document | Query refinement iterations |
| Source list with assessments | Real-time source evaluation |
| Gap analysis | Clarifying questions |
| Confidence-marked findings | Follow-up investigations |

### File Naming

Pattern: `{topic}-research-{date}.md`
Example: `competency-frameworks-research-2025-01-15.md`

### Relationship to Research Persistence Section

The "Research Persistence" section above describes WHAT to store. This section operationalizes WHERE and HOW - ensuring the skill checks for configured locations, asks the user when needed, and writes output consistently.

## Source Framework

Derived from: `frameworks/research/research-framework.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jwynia) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
