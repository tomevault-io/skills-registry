---
name: documentation-architect
description: Maintains Functional Docs (Use Cases/Sequences), Test Plans, and Architectural records. Use when this capability is needed.
metadata:
  author: adnankhan010
---

# Documentation Architect Skill

## Purpose
To maintain a "Living Documentation" system that accurately reflects the current state of the codebase, focusing on functional specifications, test plans, and architectural records.

## Rules

1.  **Format:**
    *   All documentation must be in Markdown format.
    *   Use **Mermaid JS** for all diagrams (sequence diagrams, flowcharts, class diagrams).

2.  **Functional Documentation (`apps/docs/src/content/functional/<feature>.md`):**
    *   **Actors:** List all actors involved (e.g., User, Admin, System).
    *   **Use Cases:** List the primary use cases for the feature.
    *   **Sequence Diagram:** REQUIRED. Must show the interaction flow between:
        *   User
        *   Client (UI)
        *   API (Controller)
        *   Service (Business Logic)
        *   Database
    *   **Business Rules:** Explicitly list critical business logic (e.g., "User must verify email before login").

3.  **Test Plans (`apps/docs/src/content/qa/<feature>-plan.md`):**
    *   **Business Rules to Verify:** List the business rules that need testing.
    *   **Test Mapping:** Link specific business rules to the actual `.spec.ts` files and test cases that verify them.
        *   Example: "- [x] Double Lock -> `auth.service.spec.ts` (test: 'should throw if not verified')"
    *   **Coverage Status:** State the current coverage status based on existing tests (High/Medium/Low/None) or specific missing scenarios.

4.  **Architecture (`apps/docs/src/content/architecture/architecture.md`):**
    *   Always update `apps/docs/src/content/architecture/architecture.md` if a new Module, Global Guard, Middleware, or architectural pattern (like GitOps or a new DB strategy) is added.
    *   Keep the high-level system overview up to date.

5.  **Docs-as-Code Structure (Static Site Generator Compatible):**
    *   **Target:** All documentation MUST be generated in `apps/docs/src/content`.
    *   **Structure:**
        *   `apps/docs/src/content/functional/*.md` (Use Cases).
        *   `apps/docs/src/content/architecture/*.md` (ADRs, System Design).
        *   `apps/docs/src/content/qa/*.md` (Test Plans).
    *   **Frontmatter:** All Markdown files MUST have valid frontmatter (YAML block at the top) containing at least:
        ```yaml
        ---
        title: Title of the Document
        description: Brief description
        ---
        ```

## Workflow
1.  **Analyze:** Read the code (`view_file`) to understand the actual implementation.
2.  **Document:** Create or update the functional doc in `apps/docs/src/content`.
3.  **Plan/Verify:** Create or update the QA plan in `apps/docs/src/content`, checking if tests exist for the rules.
4.  **Architect:** Update global architecture notes in `apps/docs/src/content` if necessary.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adnankhan010) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
