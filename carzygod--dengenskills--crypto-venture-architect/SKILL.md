---
name: crypto-venture-architect
description: Use when working with an end-to-end agentic workflow to research existing crypto projects, synthesize paradigms, generate new unique project proposals with implementation plans, and verify their uniqueness against market competitors.
metadata:
  author: carzygod
---

# Crypto Venture Architect Workflow

This skill guides the agent to act as a "Crypto Venture Architect," automating the process of market research, idea generation, verification, and refinement.

## Input
- A list of URLs or a file path containing URLs (e.g., `project_list.md`) to research.
- Target Ecosystem (e.g., Solana, BSC, Base).
- (Optional) Number of projects to generate (default: 5).

## Workflow Steps

### 1. Research & Analyze
**Goal**: Understand the current market landscape.
- **Action**: 
  - Read the input list of URLs.
  - For each URL, use the `read_browser_page` or `read_url_content` tool to access the site.
  - Create a summary file for each project in the `projects/` directory (e.g., `projects/project_name.md`).
  - **Summary Structure**:
    - **Concept**: One-sentence high concept.
    - **Mechanism**: How it works (Tokenomics, User Flow).
    - **Innovation**: What makes it different?
    - **Narrative**: The "vibe" or story it sells.

### 2. Synthesis (Meta-Analysis)
**Goal**: Identify the "Meta" (winning strategies).
- **Action**:
  - Review all summaries from Step 1.
  - Identify common patterns, recurring themes, and gaps in the market.
  - Write a synthesis report to `analysis/readme.md` (or `readme.md`) categorizing the findings into "Paradigms".

### 3. Ideation (The Spark)
**Goal**: Generate new, actionable project concepts.
- **Action**:
  - Based on the paradigms from Step 2, brainstorm [N] new project ideas.
  - **Constraint**: Projects must tackle a new angle or combine existing paradigms in a novel way.
  - **Output**: Create a draft file for each idea in a `drafts/` or `new/` directory.
  - **Draft Structure (in Target Language, e.g., Chinese)**:
    - **Name & Headline**: Catchy name.
    - **Ecosystem**: Targeted chain.
    - **Category**: e.g., DeFi, SocialFi, GameFi.
    - **Concept**: The elevator pitch.
    - **Core Features**: 3 bullet points.
    - **Implementation Path**: 3-Phase rough plan.

### 4. Verification (The Filter)
**Goal**: Ensure uniqueness and avoid collisions.
- **Action**:
  - For each draft project:
    - Perform a Google Search for the project name and its core keywords (e.g., `"ProjectName" crypto`, `"ProjectName" solana`, `[Core Feature Keywords]`).
    - **Analyze Results**:
      - **Direct Collision**: Same name + Similar concept -> **FAIL**.
      - **Name Collision**: Same name + Different concept -> **FAIL** (Need rename).
      - **Concept Collision**: Different name + Identical concept -> **FAIL** (Need pivot).
      - **No Conflict**: -> **PASS**.

### 5. Refinement & Finalization
**Goal**: Polish the survivors.
- **Action**:
  - For projects that **FAILED** verification:
    - **Rename**: Choose a new, unique name.
    - **Refine/Pivot**: Tweak the concept to differentiate it from the found competitor.
    - Re-verify if necessary.
  - For projects that **PASSED**:
    - Move the final markdown file to a `unique/` directory.
    - Ensure the "Implementation Path" is detailed.
  - **Notify User**: Present the list of unique, verified projects in the `unique/` directory.

## Success Criteria
- All generated projects in `unique/` have passed a web search check.
- Each project has a distinct value proposition.
- Implementation paths are practical.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/carzygod) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
