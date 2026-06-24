---
name: elixir-phoenix-docker-setup-cursorrules-prompt-fil-cursorrules
description: Apply for elixir-phoenix-docker-setup-cursorrules-prompt-fil. --- description: Provides guidelines for generating conventional commit messages based on changes in the codebase. globs: **/* Use when this capability is needed.
metadata:
  author: Tryboy869
---

# elixir-phoenix-docker-setup-cursorrules-prompt-fil

---
description: Provides guidelines for generating conventional commit messages based on changes in the codebase.
globs: **/*
---
## Commit Message Guidelines:

- Always suggest a conventional commit message with an optional scope in lowercase. Follow this structure:
  [optional scope]: [optional body][optional footer(s)]

Where:

- **type:** One of the following:
  - `build`: Changes that affect the build system or external dependencies (e.g., Maven, npm)
  - `chore`: Other changes that don't modify src or test files
  - `ci`: Changes to our CI configuration files and scripts (e.g., Circle, BrowserStack, SauceLabs)
  - `docs`: Documentation only changes
  - `feat`: A new feature
  - `fix`: A bug fix
  - `perf`: A code change that improves performance
  - `refactor`: A code change that neither fixes a bug nor adds a feature
  - `style`: Changes that do not affect the meaning of the code (white-space, formatting, missing semi-colons, etc)
  - `test`: Adding missing tests or correcting existing tests

- **scope (optional):** A noun describing a section of the codebase (e.g., `fluxcd`, `deployment`).

- **description:** A brief summary of the change in present tense.

- **body (optional):** A more detailed explanation of the change.

- **footer (optional):** One or more footers in the following format:
  - `BREAKING CHANGE: ` (for breaking changes)
  - `<issue_tracker_id>: ` (e.g., `Jira-123: Fixed bug in authentication`)

---
> Source: [Tryboy869/dojutsu-for-ai](https://github.com/Tryboy869/dojutsu-for-ai) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
