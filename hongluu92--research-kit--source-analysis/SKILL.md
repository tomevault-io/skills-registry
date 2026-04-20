---
name: source-analysis
description: Methodology for extracting and evaluating insights from scientific papers and code repositories Use when this capability is needed.
metadata:
  author: hongluu92
---

# Source Analysis & Evaluation Skill

## Overview
A systematic approach to reading, dismantling, and evaluating scientific papers and code repositories to extract core contributions and assess quality.

## 1. Academic Paper Analysis

### Reading Strategy: The Three-Pass Approach

#### Pass 1: The Bird's Eye View (5-10 mins)
1.  **Title, Abstract, and Keywords**: Determine relevance.
2.  **Introduction**: Identify the problem statement and research gap.
3.  **Headings**: Scan structure.
4.  **Conclusion**: Read the Summary/Conclusion to see the main claim.

#### Pass 2: The Content (1 hour)
1.  **Figures and Tables**: Look at the data first. Do the captions make sense?
2.  **Methodology**: How did they solve the problem?
3.  **Experiments**: What benchmarks were used?

### Paper Quality Evaluation
- **Venue**: Top-tier conference (NeurIPS, ICML, CVPR) or high-impact journal?
- **Citations**: Is it highly cited relative to its age?
- **Rigor**: Are experiments reproducible? Are baselines strong?

### Extraction Framework: PICO-C
- **P**opulation/Problem: What is being studied?
- **I**ntervention/Idea: What is the new proposed method?
- **C**omparison: What are they comparing against?
- **O**utcome: What were the results?
- **C**ontext: Limitations and assumptions.

## 2. Code Repository Evaluation (GitHub)

### Relevance Check
- Does the README clearly state the problem and solution?
- Is it a standalone implementation or just a collection of scripts?

### Quality Signals
- **Stars/Forks**: High numbers often indicate community trust (but check for bot inflation).
- **Activity**: When was the last commit? Is it actively maintained?
- **Documentation**: easy setup steps? Example usage?
- **License**: Is there a permissive license (MIT, Apache 2.0)?

### Code Inspection
- **Structure**: Is the code modular?
- **Dependencies**: Are they modern and pinned?
- **Tests**: Are there unit tests? (`tests/` folder)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hongluu92) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
