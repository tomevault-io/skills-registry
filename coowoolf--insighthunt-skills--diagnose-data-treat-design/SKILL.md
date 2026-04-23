---
name: diagnose-with-data-treat-with-design
description: Use data to establish business observability (diagnosing problems) but use creative design thinking to solve them (treatment). Resolves the conflict between data-driven and intuition-driven product development. Use when this capability is needed.
metadata:
  author: coowoolf
---

# Diagnose with Data, Treat with Design

> "You want to diagnose with data and treat with design. Data is not a tool that's going to tell you what you should build." — Julie Zhuo

## What It Is

Data should be used to establish the "observability" of a business—understanding what is actually happening (**diagnosing**). However, data cannot dictate the solution; that requires creative empathy and design thinking (**treatment**).

## When To Use

- Teams stuck in **"analysis paralysis"**
- Designers **resist data** because they feel it stifles creativity
- Leadership expects data to **tell them what feature to build**
- Product decisions are being made on **"vibes"** alone

## Core Principles

### 1. Data Reflects Reality
Use metrics to understand user behavior and spot anomalies, not to predict the future with certainty.

### 2. Diagnosis vs. Treatment

| Phase | Tool | Output |
|-------|------|--------|
| **Diagnosis** | Quantitative data | WHERE the problem is |
| **Treatment** | Qualitative design | HOW to fix it |

### 3. Avoid False Precision
A/B tests have limitations and cannot replace long-term product vision.

### 4. New Context, New Metrics
As technology shifts (e.g., to LLMs), traditional metrics (clicks) must evolve to new forms (conversation quality).

## How To Apply

```
STEP 1: Build Observability Layer
└── Implement event logging
└── Create dashboards for key flows
└── Monitor anomalies in real-time

STEP 2: Diagnose with Data
└── "Conversion dropped 15% on step 3"
└── "Users abandon after 2 messages"

STEP 3: Investigate the WHY
└── User interviews
└── Session recordings
└── Support ticket analysis

STEP 4: Treat with Design
└── Brainstorm creative solutions
└── Prototype multiple options
└── Test based on hypothesis, not metric optimization
```

## Common Mistakes

❌ Expecting data to tell you exactly **what feature to build next**

❌ Ignoring data when it contradicts your "story" of the product's success

❌ Running A/B tests on everything instead of making bold design choices

## Real-World Example

Julie mentions how rapidly growing companies often run on "vibes" until growth slows. At that point, they must implement data logging to diagnose the root cause (the "why"), but the solution to re-ignite growth requires design intervention.

---
*Source: Julie Zhuo, Lenny's Podcast*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/coowoolf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
