---
name: create-book
description: Author technical book chapters combining Feynman's pedagogical style (simple analogies, first principles, plain English) with O'Reilly's technical depth (problem-solution oriented, production-ready code). Generates Jupyter notebooks for code with companion Markdown for narrative. Use when asked to write tutorials, create educational technical content, explain programming concepts, author book chapters, or build learning materials. Use when this capability is needed.
metadata:
  author: xujialiu
---

# Feynman-O'Reilly Book Author

Author technical books with Jupyter notebooks for code and Markdown for narrative.

## Core Philosophy

**Feynman Method:** Explain intuition before formalism, plain English, define jargon with analogies, never say "trivially"

**O'Reilly Standard:** Problem-solution oriented, production-ready code, basic → advanced progression

## Directory Structure

```
<book>/
├── code/
│   ├── data/
│   │   └── 01_<chapter_name>/     # Input data per chapter
│   ├── output/
│   │   └── 01_<chapter_name>/     # Code outputs per chapter
│   ├── 01_<chapter_name>.ipynb    # Jupyter notebook (preferred)
│   └── 01_<chapter_name>/         # Python files (when notebook unsuitable)
│       └── 01_<example_name>.py
└── markdown/
    ├── attachments/               # Images/figures from code
    └── 01_<chapter_name>.md       # Markdown chapter files
```

**Code format preference:**
1. **Jupyter notebooks** (preferred) - for interactive code, visualizations, data exploration
2. **Python files** - only when notebooks unsuitable (pytest, CLI tools, multi-file modules)

**Figure handling:** Save code-generated images to `markdown/attachments/` and link in Markdown:
```markdown
![Figure description](./attachments/01_intro_figure1.png)
```

## Environment Setup

```bash
# Activate conda environment
conda activate books

# Install missing dependencies
uv pip install <package>

# Format code
ruff format <book>/code/
ruff check --fix <book>/code/
```

## Chapter Structure

```markdown
# Chapter Title

## Learning Objective
[1-2 sentences: what reader will understand/build]

## Basic: [Foundation Concept]
[Core intuition, simplest case, minimal code]

## Intermediate: [Building On Basics]
[Add complexity, handle edge cases, introduce patterns]

## Advanced: [Real-World Application]
[Production concerns, optimization, integration]

## Challenge Exercise
[Hands-on task at intermediate/advanced level]
```

## Workflow

1. **Research** - WebSearch/WebFetch for APIs and best practices
2. **Create directory structure** and plan outline
3. **Write chapters** - Spawn parallel agents
4. **Verify** - Review and fix issues
5. **Format code** - Run ruff

### Step 1: Create Directory Structure

```bash
BOOK="./book-slug"
mkdir -p "$BOOK/code/data" "$BOOK/code/output" "$BOOK/markdown/attachments"
```

### Step 2: Plan Outline

```
# Book: [Title]
Directory: ./[book-slug]/

## Overview
[2-3 sentences: what reader will learn]

## Prerequisites
[List assumed knowledge]

## Chapter 1: [Title]
- Learning objective: [specific outcome]
- Key concepts: [list 3-5]
- Format: notebook | python (choose one)
- Notebook: code/01_intro.ipynb
- Markdown: markdown/01_intro.md
- Data: code/data/01_intro/ (if needed)
- Output: code/output/01_intro/ (if needed)

## Chapter 2: [Title]
...
```

### Step 3: Parallel Chapter Writing

Launch Task agents for each chapter:

```
Use Task tool with subagent_type="general-purpose":

"Write book chapter and save to specified paths.

CHAPTER ASSIGNMENT:
- Title: [from outline]
- Learning objective: [from outline]
- Chapter number: [N] with slug: [chapter_slug]
- Format: [notebook | python]

PATHS:
- Notebook: ./[book]/code/[NN]_[slug].ipynb (if notebook format)
- Python dir: ./[book]/code/[NN]_[slug]/ (if python format)
- Markdown: ./[book]/markdown/[NN]_[slug].md
- Data: ./[book]/code/data/[NN]_[slug]/
- Output: ./[book]/code/output/[NN]_[slug]/
- Attachments: ./[book]/markdown/attachments/

ENVIRONMENT:
- Use conda env 'books': conda activate books
- Install deps with: uv pip install <package>
- Format with: ruff format && ruff check --fix

GUIDELINES (see references/chapter-guidelines.md):
- Feynman style: intuition before formalism
- Structure: Learning Objective → Basic → Intermediate → Advanced → Challenge
- Save figures to attachments/, link in Markdown as ./attachments/[NN]_[slug]_figure1.png

NOTEBOOK CELLS:
- Each cell must run independently (all imports/definitions included)
- Mirror Markdown structure
- Visualizations need axis labels and titles

Save all files to appropriate directories."
```

### Step 4: Verification

Spawn verification agents:

```
Use Task tool with subagent_type="general-purpose":

"Review chapter at ./[book]/code/[chapter] and ./[book]/markdown/[chapter].md

CHECKLIST:
1. Structure: Learning Objective → Basic → Intermediate → Advanced → Challenge
2. Feynman style: analogies before formalism, no 'trivially'
3. Code cells run independently (if notebook)
4. Figures saved to attachments/ and linked correctly
5. Data paths use code/data/[chapter]/
6. Output paths use code/output/[chapter]/

Fix issues directly. Report what was checked and fixed."
```

### Step 5: Format Code

```bash
conda activate books
ruff format ./[book]/code/
ruff check --fix ./[book]/code/
```

For detailed chapter writing guidelines, see [references/chapter-guidelines.md](references/chapter-guidelines.md).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xujialiu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
