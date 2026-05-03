---
name: geo-content-gen
description: Generates and audits content for Generative Engine Optimization (GEO) to maximize AI citation and retrieval.
version: 1.0.0
metadata:
  short-description: GEO Content Generator & Auditor
  author: GEO Skills Team
  tags: [seo, geo, ai-content, audit]
---

# Capabilities

This skill provides two main capabilities designed to help content rank better in Generative Engines (like ChatGPT, Perplexity, Gemini):

1.  **Generate GEO Content**: Creates structured, fact-dense content modules (Snippet, Table, Context, FAQ) optimized for RAG and LLM citation.
2.  **Audit Website for GEO**: Analyzes web content to determine its "AI-friendliness" score (0-100) and provides actionable improvement suggestions.

# Usage Instructions

## 1. Content Generation Mode
Use this when the user needs to create new content or rewrite existing content to be "AI-readable".

**Input Parameters:**
- `topic`: The product, entity, or concept name.
- `target_audience`: The intended readership.
- `key_facts`: A list of verifiable facts/features (no marketing fluff).

**Output:**
A JSON object containing 4 modules:
- `module_1_snippet`: Definitional snippet.
- `module_2_comparison`: Comparison table data.
- `module_3_analysis`: Functional analysis.
- `module_4_faq`: Q&A pairs.

## 2. Audit Mode
Use this when the user wants to evaluate an existing URL or text block.

**Input Parameters:**
- `url`: The source URL (identifier).
- `website_content`: The raw text to analyze.

**Output:**
A JSON audit report containing:
- `geo_score`: 0-100.
- `dimensions_analysis`: Scores and fixes for 7 dimensions (Clarity, Structure, Neutrality, etc.).
- `actionable_roadmap`: Step-by-step fix list.

# How to Apply the Results

## 1. Using the Content Strategy Report
The goal is to plant structured data where AI bots crawl most frequently.

1.  **Deploy Identity**: Place the 'AI Persona Definition' in your website's `<meta name="description">` and the first paragraph of your Home/About page. This is your "canonical truth".
2.  **Structure Content**: Use the 'Target User Questions' as `<h2>` headers in your blog posts or landing pages. Answer them directly using the 'Optimized Answer'.
3.  **Publish Externally**: Follow the 'Placement Strategy' to post on recommended platforms (GitHub, Reddit, StackOverflow). These high-authority domains are training data sources for LLMs.

## 2. Using the Audit Report
The goal is to remove "marketing fluff" that causes AI hallucinations or rejections.

1.  **Fix Critical Failures**: Address any red flags immediately (e.g., missing definition, excessive adjectives).
2.  **Optimize Content**: Rewrite the sections quoted in 'Detailed Analysis' using the specific 'Fix' recommendations.
3.  **Re-Audit**: After changes, run this skill again to verify the GEO Score improvement. Aim for a score > 80.

# Implementation Details

- **Core Logic**: `scripts/index.js` (Class `GeoContentGenerator`)
- **Prompt Templates**: 
  - Generation: `assets/geo_prompt.md`
  - Audit: `assets/geo_audit_prompt.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/holdlijun) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
