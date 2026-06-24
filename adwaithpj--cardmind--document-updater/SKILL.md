---
name: document-updater
description: Updates the README.md file to reflect the current state of the project. Use this skill when the user says "Update Documentations" or wants to keep the README in sync with GEMINI.md and the actual codebase. Use when this capability is needed.
metadata:
  author: adwaithpj
---

# Document Updater

A specialized skill for maintaining and updating the project's `README.md` file to ensure it accurately reflects the latest architectural decisions, tech stack, features, and setup instructions.

## Workflow

1. **Information Gathering**:
   - Read `GEMINI.md` as the primary source of truth for project context and standards.
   - Read `package.json` to verify current dependencies, versions, and scripts.
   - Read `prisma/schema.prisma` (or relevant ORM schema) to confirm the database structure.
   - Scan the `src/` directory if necessary to understand the actual file structure.

2. **Analysis**:
   - Compare the current `README.md` with the gathered information.
   - Identify outdated sections, incorrect tech stack details, or missing features.
   - Ensure that setup instructions (environment variables, commands) are accurate.

3. **Execution**:
   - Rewrite or patch `README.md` with the corrected information.
   - Maintain a consistent, professional, and concise tone.
   - Use clear headings, tables, and code blocks for readability.

4. **Validation**:
   - Verify that all links and commands in the updated `README.md` are valid within the project context.
   - Check for any remaining discrepancies.

## Constraints
- Do NOT add "just-in-case" information. Keep it focused on what's actually in the project.
- Follow the existing style and formatting of the `README.md`.
- Prioritize accuracy over verbosity.

---
> Source: [adwaithpj/CardMind](https://github.com/adwaithpj/CardMind) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
