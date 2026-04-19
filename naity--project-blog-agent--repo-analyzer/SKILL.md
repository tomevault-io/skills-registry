---
name: repository-analyzer
description: Deeply analyze a code repository to extract architecture, design decisions, tech stack, and interesting implementation details for blog posts Use when this capability is needed.
metadata:
  author: naity
---

# Repository Analyzer Skill

This skill enables deep analysis of code repositories to gather insights for writing technical blog posts.

## When to Use This Skill

Use this skill when you need to:
- Understand a project's architecture and design
- Extract the tech stack and dependencies
- Identify interesting implementation patterns
- Find code examples worth highlighting in a blog post
- Discover story angles and unique aspects of the project

## What This Skill Analyzes

### 1. Project Overview
- Main purpose and value proposition
- Target users and use cases
- Key features and functionality
- Project status and maturity

### 2. Architecture & Design
- Overall system architecture
- Major components and their relationships
- Design patterns used
- Architecture diagrams (if present)
- Key design decisions and trade-offs

### 3. Tech Stack
- Programming languages
- Frameworks and libraries (from dependency files)
- External services and integrations (AWS, APIs, etc.)
- Development tools and build systems

### 4. Code Structure
- Directory organization
- Key files and their purposes
- Module/package structure
- Interesting implementation details

### 5. Story Angles
- What makes this project unique?
- Interesting technical challenges solved
- Non-obvious design choices
- Lessons learned (from docs, commit messages)
- "Aha" moments worth sharing

## How to Use

```bash
# Analyze a repository
# The skill will examine README, docs, code structure, and dependencies

# Example analysis workflow:
1. Read README.md, ARCHITECTURE.md, docs/
2. List directory structure (tree -L 2 or ls -la)
3. Examine dependency files (package.json, requirements.txt, pyproject.toml)
4. Grep for interesting patterns (TODO, NOTE, IMPORTANT)
5. Read key source files
6. Check git log for interesting commits (optional)
```

## Output Format

Create a comprehensive analysis document named `repo_analysis.md` with:

```markdown
# Repository Analysis: [Project Name]

## Executive Summary
- What: [1-2 sentence description]
- Why: [Problem it solves]
- For whom: [Target audience]
- Unique aspect: [What makes it interesting]

## Project Purpose & Value
[Detailed description of what the project does and why it matters]

## Architecture Overview
[High-level architecture description]
- Key components
- Data flow
- External dependencies
[Reference any architecture diagrams found]

## Tech Stack
### Languages
- [Language 1]: [Usage/purpose]

### Frameworks & Libraries
- [Framework]: [Version, purpose]

### External Services
- [Service]: [Integration purpose]

### Development Tools
- [Tool]: [Purpose]

## Key Implementation Details

### [Interesting Pattern/Feature 1]
**Location:** [file path]
**Description:** [What it does]
**Why interesting:** [Why this is blog-worthy]
**Code example:** [Brief snippet or reference]

### [Interesting Pattern/Feature 2]
[Same structure]

## Story Angles & Blog Hooks

### Primary Angle
[Best story angle - "How I built...", "Why I chose...", etc.]

### Alternative Angles
1. [Alternative angle 1]
2. [Alternative angle 2]

### Interesting Challenges
- [Challenge 1 and how it was solved]
- [Challenge 2 and how it was solved]

## Blog-Worthy Code Examples
1. [Example 1]: File path and brief description
2. [Example 2]: File path and brief description

## Lessons Learned & Insights
[From README, docs, or code comments]

## Recommended Blog Structure
Based on this analysis, suggest:
- Target audience
- Tone (deep technical vs accessible)
- Key sections to cover
- Estimated reading time
```

## Tips for Effective Analysis

1. **Start Broad, Then Deep**
   - Read README and docs first
   - Get directory structure overview
   - Then dive into specific files

2. **Look for Documentation**
   - ARCHITECTURE.md, DESIGN.md
   - docs/ folder
   - Code comments with explanations
   - Inline documentation

3. **Find the "Why"**
   - Don't just list what's there
   - Understand design decisions
   - Identify trade-offs

4. **Think Like a Blogger**
   - What would make developers click?
   - What's surprising or non-obvious?
   - What can readers learn?

5. **Capture Specific Examples**
   - Note file paths for later reference
   - Copy interesting code snippets
   - Reference specific line numbers if helpful

## Common Sources of Insight

- `README.md` - Project overview, features
- `ARCHITECTURE.md` - System design
- `package.json`, `requirements.txt`, `pyproject.toml` - Dependencies
- `.env.template`, `config/` - Configuration patterns
- `src/`, `lib/` - Core implementation
- `tests/` - How the system is tested
- `.github/workflows/` - CI/CD patterns
- `docs/` - Detailed documentation

## Example Usage

For a Claude Agent SDK project:
1. Identify it uses Claude Agent SDK (from dependencies)
2. Find custom skills in `.claude/skills/`
3. Analyze prompt engineering patterns
4. Check for multi-agent patterns
5. Look for interesting integrations (AWS, etc.)
6. Find the core workflow in main files
7. Identify the "hook": What problem does this solve uniquely?

Output: Comprehensive analysis that enables writing an engaging, technically accurate blog post.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/naity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
