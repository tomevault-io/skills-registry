---
name: deep-researcher
description: > Use when this capability is needed.
metadata:
  author: fangdav
---

# Deep Researcher

Take a segment and produce a detailed, standalone deep-dive document. Typically follows the analyzer — drilling into the top-ranked options to build thorough, evidence-based cases for each.

## When to Use

- The analyzer has surfaced top-ranked options worth investigating further
- A research .md file exists with high-level segments that need more detail
- A topic needs more than a few bullets — it needs thorough, sourced analysis

## Relationship to Other Research Skills

The deep researcher can work standalone or as part of the research workflow:
- Typically follows **analyzer** — takes the top-ranked segments and investigates them in depth
- Typically preceded by **research-md-maker** — which established the broad landscape
- Feeds into **research-synthesizer** — deep dives become source material for the final .docx

Every deep-dive document must **hyperlink back to the parent research file and comparison matrix** (if they exist).

## Execution Flow

### Step 1: Identify Source Files and Confirm Segments

1. Locate the parent research .md file in the shared drive
2. Check for a comparison matrix (`.md` or `.xlsx`) from the analyzer — if one exists, read the rankings to understand which segments scored highest
3. Present the available segments to the user and **confirm which segment(s) they want to deep-dive** before proceeding
4. Read the relevant segment(s) from the parent file to understand what's already been covered
5. Note the parent file path and comparison matrix path — both will be linked in the output

If no parent file exists, ask the user if they want to run research-md-maker first to establish the landscape, or proceed directly with the deep dive.

### Step 2: Scope the Deep Dive

For each selected segment, clarify:
- What specific questions should this deep dive answer?
- Any particular angle? (technical, financial, competitive, regulatory)
- How will this research be used? (decision-making, investor deck, product spec)

### Step 3: Research

Go deep. Unlike the broad research file, this is thorough:
- Search for primary sources: academic papers, whitepapers, official reports, company data
- Look for case studies, real-world examples, and precedents
- Find quantitative data: market sizes, growth rates, adoption numbers, financials
- Identify expert opinions and contrarian viewpoints
- Map the competitive or option landscape within this specific segment

### Step 4: Write the Deep-Dive Document

Structure:

```markdown
# [Segment Name] — Deep Dive

**Author:** [Name]
**Date:** [YYYY-MM-DD]
**Parent Research:** [Hyperlink to the parent research .md file]
**Comparison Matrix:** [Hyperlink to the comparison matrix .md or .xlsx, if it exists]

## Context

[2-3 sentences. What is this segment, why does it matter, and what question
are we answering. Link back to the parent research file for the full landscape.]

## Background

[Longer treatment than the parent file. History, evolution, how this segment
emerged. Establish the foundation a reader needs to understand what follows.]

## Current State

[What exists today. Key players, market size, adoption levels, maturity.
Include data with Vancouver citations.]

## How It Works

[Mechanics, technical details, or operational model — whatever "how it works"
means for this segment. Diagrams or tables where helpful.]

## Strengths & Opportunities

[Detailed analysis, not just bullet points. Each strength backed by evidence.]

## Weaknesses & Risks

[Honest assessment. What could go wrong. What are the limitations.
Include counterarguments to the strengths above.]

## Case Studies

[2-3 real-world examples of this segment in action.
For each: what happened, what worked, what didn't, what's the takeaway.]

### [Case Study 1: Name]
- **Context:** [Situation]
- **Approach:** [What they did]
- **Outcome:** [Result]
- **Takeaway:** [Lesson for Pocket Space]

### [Case Study 2: Name]
[Same structure]

## Relevance to Pocket Space

[Detailed analysis of how this segment connects to Pocket Space specifically.
Not a one-liner — a genuine assessment of applicability, risks, and next steps
if the team wanted to pursue this direction.]

## Key Takeaways

- [5-8 bullets summarizing the most important findings]

## Open Questions

- [What this deep dive didn't fully answer]
- [What would require further investigation, interviews, or testing]

## References

[Vancouver style — numbered list]
[1] Author. Title. Source. Year. URL.
[2] ...
```

### Step 5: File Placement

Deep-dive files live inside the `Research/` folder of the topic folder, nested under a segment subfolder.

- If a topic folder structure exists (`[Topic_of_Research]/Research/`), place the deep dive inside a segment subfolder:
  ```
  [Topic_of_Research]/
  ├── [Topic_of_Research].docx          ← Added later by research-synthesizer
  └── Research/
      ├── README.md
      ├── Landscape_Research.md          ← Parent research file
      ├── [Segment_A]/                   ← Segment subfolder for this deep dive
      │   └── [Segment_A]_Deep_Dive.md   ← This file goes here
      ├── [Segment_B]/
      │   └── [Segment_B]_Deep_Dive.md
      └── ...
  ```
- If no topic folder exists yet, create the structure:
  ```
  [Department_Folder]/
  └── [Topic_of_Research]/
      └── Research/
          ├── README.md
          └── [Segment_Name]/
              └── [Segment_Name]_Deep_Dive.md
  ```
- Segment subfolders can themselves be deeply nested if the deep dive spawns further sub-research
- Name: `[Segment_Name]_Deep_Dive.md`
- Follow naming conventions: underscores, descriptive name
- Follow author sign-off rules from contribution guidelines
- Update the `Research/README.md` to reflect the new file and subfolder

### Step 6: Hyperlinks

The finished document must include:
1. **Link to parent research file** — In the header metadata and in the Context section, hyperlink to the .md file from research-md-maker that this deep dive expands on (use relative path within the `Research/` folder)
2. **Link to comparison matrix** — If a comparison matrix (`.md` or `.xlsx`) exists from the analyzer, hyperlink to it in the header metadata
3. **Links to sibling deep dives** — If other segments from the same parent file have also been deep-dived, cross-link to those documents within neighboring segment subfolders
4. **Update the parent file** — Add a note in the parent research .md under the relevant segment: `See also: [Segment_Deep_Dive.md](link)` pointing to this new document

## Sources & Citations (Vancouver Style)

Use **Vancouver citation style** — numbered references in order of first appearance.

**Inline:** Use bracketed numbers where a claim is made.
- Example: `Token-gated communities saw 3x higher retention than open-access alternatives [1], with the strongest effects in groups under 150 members [2].`

**Rules:**
- Every factual claim, statistic, or finding must have a numbered citation
- Numbers assigned in order of first appearance
- Unsourced findings must be marked `[Author's analysis]`
- Prefer primary sources over secondhand summaries
- Include URLs for all web-accessible sources

## Writing Style

Unlike research-md-maker (which is parsimonious), deep-dive documents are **thorough**:
- Longer paragraphs are acceptable when building an argument
- Include context and explanation, not just conclusions
- Use data and examples to support points
- Still no filler — every paragraph should advance understanding
- Maintain a professional, analytical tone

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fangdav) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
