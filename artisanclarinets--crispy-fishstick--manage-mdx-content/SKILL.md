---
name: manage-mdx-content
description: How to author, structure, and manage MDX content for Work (case studies) and Insights (articles). Use when creating new content, updating frontmatter, or ensuring MDX pipeline compliance. Use when this capability is needed.
metadata:
  author: artisanclarinets
---

# Manage MDX Content

This skill defines the strict content authoring standards for Project SENTINEL. It ensures that all engineering narratives and architectural insights are technically accurate, properly structured, and visually consistent.

## Quick Start

Create a new MDX article in 4 steps:

1.  **Choose Location**: `content/work/*.mdx` for case studies, `content/insights/*.mdx` for articles.
2.  **Define Frontmatter**: Fill in all required fields (title, description, date).
3.  **Author Content**: Use standard Markdown with custom MDX components (Callout, Metric, etc.).
4.  **Verify Rendering**: Check the local development server to ensure the MDX pipeline parses the file correctly.

```mdx
---
title: "Optimizing High-Frequency Trading APIs"
description: "How we reduced P99 latency by 40% using Next.js 16."
date: "2026-01-13"
tags: ["Performance", "Next.js", "API"]
---

# Overview

This project involved a complete overhaul of the core trading engine...

<Metric label="Latency Reduction" before="120ms" after="72ms" delta="-40%" />
```

## Core Concepts

### 1. Content Types
*   **Work (Case Studies)**: Deep-dive engineering narratives demonstrating system design, performance, and failure modes. Tone: Technical, evidence-based.
*   **Insights (Articles)**: Thought leadership, architectural patterns, and industry analysis. Tone: Professional, authoritative.

### 2. Strict Frontmatter Validation
Our MDX pipeline enforces schema validation. Missing required fields will cause build failures.
*   **Required**: `title`, `description`, `date`.
*   **Recommended**: `tags`, `readTime`, `image`.

### 3. Interactive "Case Modes"
The `/work/[slug]` page supports interactive narratives. Structure your MDX with clear headings to support:
1.  **Overview**: Problem and context.
2.  **Architecture**: System diagrams and technical decisions.
3.  **Failure Modes**: Incident analysis and fixes.
4.  **Results**: Hard metrics and KPIs.

## Content Authoring Workflows

### Adding a New Case Study

**Step 1: Initialize File**
Create `content/work/my-project.mdx`.

**Step 2: Populate KPIs**
Use the `kpis` frontmatter field for structured data and the `<Metric />` component for inline highlights.

**Step 3: Handle Redactions**
Use the `redactions` frontmatter field to document removed sensitive info and the `<RedactionNote />` component in the text.

```yaml
redactions:
  - label: "Client Name"
    reason: "NDA"
```

### Managing Assets
*   **Images**: Place in `public/images/work/` or `public/images/insights/`.
*   **References**: Use absolute paths (e.g., `/images/work/diagram.png`).

## Advanced Patterns

### Custom MDX Components
Leverage the library of custom components in `components/mdx/` to create rich, interactive technical documentation.
*   `<SystemSpec />`: For hardware/software requirements.
*   `<ShopifySyncDiagram />`: For specific integration flows.

### SEO & Social Sharing
Ensure `description` and `image` frontmatter fields are optimized for social media previews and search engine indexing.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/artisanclarinets) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
