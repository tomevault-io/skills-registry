---
name: gemini-repo-manager
description: Use when working with a skill for managing and maintaining a project-wide `.gemini/GEMINI.md` repository manifest.
metadata:
  author: CarmeloMaitaO
---

# Gemini Repository Manager

This skill automates the maintenance of the project-wide `.gemini/GEMINI.md` file.

## Operational Workflow

When activated, this skill generates or updates the `.gemini/GEMINI.md` file at the root of the repository with the following structure:

- **Description**: High-level project overview.
- **Tech Stack**: Technologies and languages used.
- **Folder Structure**: Tree structure with symbol/file summaries.
- **Operational Context**: Environment setup and Build/Deployment instructions.
- **Key Patterns**: Applied architectural styles (e.g., MVC, Hexagonal).
- **Anti-patterns**: List of prohibited patterns/practices.
- **Definition of Done (DoD)**:
  - No statement outside `try...except`.
  - Unit tests for all procedures/methods (split by object/purpose).
  - DRY adherence.
  - All tests passing.
- **Documentation Strategy**: How documentation is maintained.
- **Glossary**: Domain-specific language used in the project.
- **Reference Links**: External documentation and resources.

## Deployment

To trigger the update, run the task associated with this skill in an active session.

---
> Source: [CarmeloMaitaO/dotfiles](https://github.com/CarmeloMaitaO/dotfiles) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
