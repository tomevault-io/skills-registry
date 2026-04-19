---
name: research
description: Find relevant scientific papers for a given research prompt, analyze their approaches, and provide structured summaries with a general methodology overview. Use when this capability is needed.
metadata:
  author: eriknovak
---

# Research Skill

Find, analyze, and summarize scientific papers relevant to a research prompt. Produces structured per-paper summaries and a general methodology overview.

## Usage

- `/research` - Start interactive research session (prompts for topic)
- `/research "cross-lingual document retrieval using embedding models"` - Research a specific topic
- "Find papers on low-resource NER"
- "Research recent approaches to knowledge distillation for LLMs"

## Workflow

### Step 1: Extract Topics

From the user's prompt, identify 3-6 specific research topics or keywords that capture the core interests. Present these as interactive checkboxes using `AskUserQuestion` so the user can confirm, deselect, or add topics.

Example:

```
From your prompt, I identified these topics. Which ones should I search for?

[ ] Cross-lingual retrieval
[ ] Dense passage embeddings
[ ] Multilingual language models
[ ] Document ranking
[ ] Zero-shot transfer
```

Wait for user confirmation before proceeding. If the user adds custom topics, incorporate them.

### Step 2: Search for Papers

Search for papers matching the confirmed topics. Use `WebSearch` with academic queries.

**Search strategy:**
- Combine confirmed topics into 2-4 targeted queries
- Prefer peer-reviewed venues: ACL, EMNLP, NAACL, NeurIPS, ICML, ICLR, AAAI, CVPR, ICCV, ECCV, IEEE, Elsevier, Springer
- Include venue/journal names in queries to bias toward peer-reviewed work
- Add "paper" or "proceedings" to queries
- Search for recent work (last 2-3 years) and seminal/highly-cited work
- Aim to find 5-10 relevant papers

**Query patterns:**
- `"topic1 topic2" site:aclanthology.org OR site:arxiv.org OR site:openreview.net`
- `"topic1" "topic2" conference paper 2024 2025`
- `"topic1" survey OR review journal`

For each paper found, fetch its abstract and metadata using `WebFetch` when possible.

### Step 3: Assess Relevancy

For each paper, assign a relevancy score on a 1-5 scale based on how well it matches the confirmed topics:

| Score | Meaning |
|-------|---------|
| 5 | Directly addresses the core prompt; covers multiple confirmed topics |
| 4 | Highly relevant; addresses most confirmed topics |
| 3 | Moderately relevant; covers some topics or a closely related area |
| 2 | Tangentially relevant; shares methodology or domain but different focus |
| 1 | Loosely related; only peripherally connected to the prompt |

Sort papers by relevancy score (highest first). Only include papers scoring 3 or above in the final output. Mention excluded papers briefly if they were borderline.

### Step 4: Summarize Each Paper

For each paper (sorted by relevancy), provide a structured summary using this exact format:

```markdown
## Paper Title (Year)
**Authors:** Author list
**Venue:** Conference/Journal name
**Relevancy:** X/5

### Motivation
- Bullet point 1
- Bullet point 2

### Problem to Solve
- Bullet point 1
- Bullet point 2

### Methodology
- Bullet point 1
- Bullet point 2
- Bullet point 3

### Datasets & Metrics
- Bullet point 1 (dataset details)
- Bullet point 2 (metrics used)

### Results & Insights
- Bullet point 1
- Bullet point 2
- Bullet point 3
```

**Summary rules:**
- Keep each section to 2-4 bullet points
- Be concise: one idea per bullet, no filler
- Use concrete numbers for results when available
- For methodology, focus on what makes their approach novel
- For results, highlight comparisons to baselines

### Step 5: General Methodology Summary

After all individual summaries, provide a **General Summary** section that synthesizes the findings across all papers. This should NOT reference specific paper names or methods. Instead, describe general patterns and effective strategies.

```markdown
# General Summary

## Effective Approaches
- General description of what types of changes/methods work well
- Patterns observed across successful approaches

## Key Factors
- What aspects of the problem seem most important to address
- Common components in high-performing solutions

## Open Challenges
- What problems remain unsolved
- Where current approaches fall short

## Recommendations
- Based on the surveyed work, what directions seem most promising
- What combinations of techniques appear effective
```

**General summary rules:**
- Abstract away from specific paper names: say "approaches that incorporate X" not "Method Y from Paper Z"
- Focus on transferable insights: "adapting pre-trained representations to the target domain improves..." not "BERT-based fine-tuning gave 3% improvement"
- Identify convergent trends across papers
- Highlight disagreements or contrasting findings
- Keep each section to 3-5 bullet points

## Output Format

The complete output should follow this structure:

```markdown
# Research Summary: [Topic from prompt]

**Topics searched:** topic1, topic2, topic3
**Papers found:** N total, M included (relevancy >= 3)
**Date of search:** YYYY-MM-DD

---

[Individual paper summaries, sorted by relevancy score]

---

# General Summary

[Synthesized methodology overview]
```

## Tips

- If a paper's full text is not accessible, summarize based on the abstract and any available information
- When results are ambiguous or the abstract lacks detail, note this explicitly
- Prefer papers from top-tier venues when relevancy scores are equal
- Include survey/review papers if found, as they provide broader context
- If fewer than 3 papers are found, suggest broadening or adjusting the topics

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eriknovak) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
