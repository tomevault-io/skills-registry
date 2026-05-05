---
name: generate-standard-readme
description: Governance-focused README with fixed structure and output contract. Use for asset governance, audit, or standardized first-impression docs. For process-driven creation (templates by project type) use crafting-effective-readmes. Use when this capability is needed.
metadata:
  author: neversight
---

# Skill: Generate Standard README

## Purpose

Create **professional, consistent, highly readable** front-page documentation for **any software project** (open source, internal services, microservices, tooling). A standardized information layout reduces collaboration cost, improves engineering norms, and keeps core assets discoverable.

---

## Use Cases

- **New project**: Quickly add a standard README for a new repo.
- **Asset governance**: Unify README style across internal services or libraries for better indexing and cross-team discovery.
- **Audit and compliance**: Bring legacy systems up to documentation standards for internal audit or architecture review.
- **Handover and release**: When transferring a project, changing ownership, or releasing publicly, ensure the audience can understand purpose, usage, and how to contribute.

**When to use**: When the project needs a “first face” that explains what it is, how to use it, and how to collaborate.

---

**Scope**: This skill emphasizes a **fixed output structure** and **governance** (unified style, audit, discoverability); the output contract is embedded in the skill. For template-by-project-type or guided Q&A creation, use skills.sh’s `crafting-effective-readmes` (e.g. softaworks/agent-toolkit).

---

## Behavior

### Principles

- **Clarity**: Readers immediately understand what the project is and what problem it solves.
- **Completeness**: Include everything users and contributors need.
- **Actionable**: Provide copy-paste install and quick-start commands.
- **Professional**: Use standard Markdown and a conventional section order.

### Tone and style

- Use **neutral, objective** language; avoid hype (“The best,” “Revolutionary”) unless backed by data.
- **Direct and concise**: Short sentences; avoid filler adjectives and bureaucratic phrasing; professionalism through clarity and scannability, not formality.
- Keep code examples short and comments clear.

### Visual elements

- **Badges**: Include License, Version, Build Status, etc. at the top.
- **Structure**: Use `---` or clear heading levels to separate sections.
- **Emoji**: Use sparingly (e.g. 📦, 🚀, 📖) to improve scannability.

---

## Input & Output

### Input

- **Project metadata**: Name, one-line description.
- **Features**: Core capabilities.
- **Requirements**: e.g. Node.js/Python version.
- **Install/run**: Concrete shell commands.

### Output

- **README source**: Markdown with this structure:
  1. Title and badges
  2. Core description
  3. ✨ Features
  4. 📦 Installation
  5. 🚀 Quick start
  6. 📖 Usage / configuration
  7. 🤝 Contributing
  8. 📄 License
  9. 👤 Authors and acknowledgments

---

## Restrictions

- **No broken links**: Do not add links that 404.
- **No redundant repetition**: Do not repeat the same fact (e.g. license) in multiple sections.
- **No hardcoded paths**: Use placeholders or variables in install and quick-start examples.
- **License required**: Always include a License section; do not omit it.

---

## Self-Check

- [ ] **Three-second test**: Can a reader understand what the project does in a few seconds?
- [ ] **Closed loop**: Can someone run “Quick start” after following “Installation”?
- [ ] **Tone**: Is the text direct and concise, without bureaucratic or report-like phrasing?
- [ ] **Badges**: Do badge links point to the correct branch or file?
- [ ] **Narrow screens**: Are tables and long code blocks readable on small screens?

---

## Examples

### Before vs after

**Before (minimal)**:

> # MyProject
>
> This program processes images.
> Install: pip install .
> Run: python run.py

**After (standard)**:

> # MyProject
>
> [![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
>
> A high-performance image batch-processing tool that speeds up compression with concurrency.
>
> ---
>
> ## ✨ Features
>
> - **Concurrent compression**: Multi-threaded; faster than baseline.
> - **Formats**: WebP, PNG, JPEG conversion.
>
> ---
>
> ## 📦 Installation
>
> ```bash
> pip install my-project
> ```
>
> ---
>
> ## 🚀 Quick start
>
> ```python
> from myproject import Compressor
> Compressor('images/').run()
> ```

**Edge case: Legacy project with little info**

- **Input**: Name: legacy-auth. No description. No feature list. Environment and install unknown.
- **Expected**: Still produce a structurally complete README; use placeholders (e.g. “See source for features”, “Install steps TBD”) and mark “to be completed”; do not invent features or commands; keep badges, section order, and License so the user can fill in later.

---

## Appendix: Output contract

When this skill produces a README, it follows this contract:

| Section order | Required |
| :--- | :--- |
| 1 | Title and badges |
| 2 | Core description |
| 3 | ✨ Features |
| 4 | 📦 Installation |
| 5 | 🚀 Quick start |
| 6 | 📖 Usage / configuration |
| 7 | 🤝 Contributing |
| 8 | 📄 License |
| 9 | 👤 Authors and acknowledgments |

Restrictions: no broken links; no redundant repetition; no hardcoded paths; License section required.

---

## References

- [GitHub README docs](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-readmes)
- [Awesome README](https://github.com/matiassingers/awesome-readme)
- [Shields.io (badges)](https://shields.io/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
