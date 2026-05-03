---
name: latex-outreach-presentation
description: Instructions on building a latex beamer presentation for a scientifically literate but non-professional audience. Use this skill when asked to create a latex beamer presentation for a non-professional audience. Use when this capability is needed.
metadata:
  author: yogeshw
---

# Skill Instructions

Follow the instructions below. See example.md for latex code for handling figures


---

# SKILL: LaTeX Beamer Presentation Authoring for Scientifically Oriented Lay Audiences

## Purpose

This skill produces **clear, accurate, and visually clean LaTeX Beamer presentations** intended for **scientifically literate non-specialists**. Typical audiences include undergraduate of high school science students, educators, engineers from other fields, advanced hobbyists, policy professionals, and the general public with a strong interest in science.

The goal is **conceptual understanding without loss of correctness**, avoiding both technical overload and popular-science sensationalism.

---

## Scope

This skill applies **exclusively** to presentations created using **LaTeX Beamer**.

Supported use cases include:

* Public science lectures and outreach talks
* Interdisciplinary seminars
* Science communication events
* High-level overviews of astronomy, astrophysics, or space science

Research seminars for specialists and technical conference talks are **out of scope** for this skill.

---

## Core Output Requirements

### 1. Document Class and Compilation

* The document **must use**:

  ```latex
  \documentclass{beamer}
  ```
* Output **must compile cleanly with `pdflatex`**
* Use only standard, widely available LaTeX packages
* On the title slide only, include a logo at bottom right /home/yogesh/work/images/ncralogo.jpg which is 697x797 pixel image. 
* Ensure:

  * No LaTeX compilation errors
  * Correct math mode usage
  * No broken references or malformed frames

If a `pdflatex` error is reported, the skill **must automatically fix it** and return corrected LaTeX.

---

### 2. Beamer Theme and Visual Design

* Use a **clean, readable Beamer theme** with no navigational icons
* Use 16:9 aspect ratio for the presentation, unless otherwise specified
* Slide number should appear at bottom right but don't put the total slide number
* Use beautiful color schemes and fonts always
* Unless otherwise specified, text should be in light colors on a uniform black background
* Colors should be attractive but not gaudy
* Slides must be legible from the back of a large room
* Avoid visual clutter
* Animations and overlays may be used **sparingly** if they improve conceptual clarity, but are not required

---

### 3. Frame Structure

Each frame must:

* Have a clear, descriptive title
* Focus on **one idea or concept**
* Use short bullet points or simple diagrams
* Avoid long paragraphs or dense equations

Slides should be self-contained and understandable without prior
domain expertise.

---

### 4. Writing Style and Tone

* Clear, neutral, and explanatory
* Technically accurate but **jargon-minimized**
* Define all necessary technical terms on first use
* Avoid hype, exaggeration, or anthropomorphic language
* No conversational filler or rhetorical questions

Preferred:

> “A black hole is a region where gravity is so strong that light cannot escape.”

Disallowed:

> “Black holes are mind-blowing cosmic monsters that eat everything!”

---

### 5. Scientific Accuracy and Pedagogy

* All statements must be scientifically correct
* Use analogies **only when they aid understanding**, and explicitly state their limitations
* Clearly separate:

  * Established facts
  * Models or interpretations
  * Open questions

Approximations and simplifications must be acknowledged.

---

### 6. Mathematics and Quantitative Content

* Use mathematics **sparingly**
* Equations should:

  * Illustrate relationships, not derivations
  * Be accompanied by plain-language explanations
* Prefer proportionalities or scaling relations over full formulae
* All symbols must be defined immediately

---

### 7. Figures and Visual Explanations

* Beautiful cosmic images will be central to the talk. All images sit in a common images folder. Images should be on separate slides, where there will be no text except the title. See example.md for how images are to be formatted. Suggest suitable images by adding latex comments to image slides. Add a credit line at the bottom of all images.
* Additional figures, if needed,  should be produced using **`tikz` and/or `pgfplots`**
* Cartoon diagrams are preferred over plots unless quantitative insight is essential
* Figures must:

  * Be clearly labeled
  * Avoid unnecessary technical detail
  * Include short, explanatory captions


---

### 8. Links and Further Reading

* All links must be **clickable in the PDF**
* Use:

  ```latex
  \usepackage{hyperref}
  ```
* Links may point to:

  * Reputable educational resources
  * Observatory or mission websites
  * Public data portals
* Place links on dedicated “Further Reading” or summary slides

---

### 9. Error Handling and Self-Correction

If the user reports:

* LaTeX compilation errors
* Visual layout problems affecting readability
* Conceptual confusion or misleading explanations

The skill must:

1. Identify the issue
2. Correct the LaTeX and/or explanation
3. Return a **fully corrected, compilable Beamer document**

---

## Explicit Non-Goals

This skill must **not**:

* Produce specialist-level technical slides
* Include dense derivations or unexplained equations
* Use outreach cliches, hype, or sensational language
* Invent data or oversimplify to the point of incorrectness
* Use non-LaTeX formats

---

## Expected User Inputs

Typical inputs may include:

* Target audience background (e.g. engineers, teachers, general public)
* Talk duration or approximate slide count
* Topic and desired depth
* Requests for conceptual diagrams
* `pdflatex` error messages, if any

Ask user for clarifications, if in doubt.

---

## Output Guarantee

Every response produced by this skill must be:

* Scientifically correct
* Accessible to non-specialists
* Clearly written and well-structured
* Valid LaTeX Beamer
* Fully `pdflatex`-compilable with zero errors other than the occasional overflow
---

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yogeshw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
