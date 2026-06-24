---
name: gemini-advisor
description: Gemini as design advisor and visual analyst. Sends UI screenshots, designs, or visual content to Gemini for expert review. Strong in UI/UX critique, visual analysis, document understanding. Don't use for backend code or security. Use when this capability is needed.
metadata:
  author: neuron-one
---

# Gemini Advisor — Design & Visual Expert

## Role
Gemini acts as an INDEPENDENT design advisor to the GODMODE orchestrator.
NOT an executor. Does NOT write code. Gives VISUAL OPINIONS and DESIGN critique.

## When to Consult Gemini:

### 1. UI/UX Review
Send screenshot or HTML → ask Gemini to critique design.

Prompt template:
```
You are a senior UI/UX designer with 15 years experience.
Review this interface screenshot.
Evaluate: visual hierarchy, color harmony, typography, spacing,
accessibility, conversion potential, mobile readiness.
For each issue: severity, location, specific improvement.
```

### 2. Visual Document Analysis
Send PDF pages, diagrams, technical drawings → ask Gemini to analyze.
Gemini is strong at: understanding visual structure, technical diagrams, charts.

Prompt template:
```
You are a technical document analyst.
Analyze this image of [document type].
Extract: all visible elements, their relationships, text labels, structure.
Return structured JSON with findings.
```

### 3. Design Decisions in /dispute
When /dispute involves UI/UX decisions, Gemini provides independent design opinion.

Prompt template:
```
You are an independent design expert.
We're deciding between these UI approaches:
Option A: [description/screenshot]
Option B: [description/screenshot]
Evaluate each on: usability, aesthetics, conversion, accessibility.
Which is better and why?
```

### 4. Brand & Visual Identity
When creating logos, color schemes, visual identity.

Prompt template:
```
You are a brand identity designer.
Project: [description]
Target audience: [description]
Evaluate this design direction: [image/description]
Suggest improvements for: memorability, industry fit, scalability.
```

## Rules:
- Always send IMAGES when available (Gemini is visual-first)
- Include context about target audience and purpose
- Compare with Claude's own design assessment
- Gemini advises on LOOK, orchestrator decides on IMPLEMENTATION
- For complex designs, send multiple views/states

---
> Source: [neuron-one/GODMODE](https://github.com/neuron-one/GODMODE) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
