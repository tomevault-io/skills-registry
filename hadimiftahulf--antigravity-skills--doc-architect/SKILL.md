---
name: doc-architect
description: Automatically generates and maintains project documentation, including READMEs, API references, and Architectural Decision Records (ADR). Use when this capability is needed.
metadata:
  author: hadimiftahulf
---

# Documentation Architect (The Librarian 📚)

Code is read much more often than it is written. Keep it understandable.

## Artifacts to Maintain

### 1. README.md (The Front Door)
- **What**: Project overview, setup instructions, and architecture summary.
- **Check**: Is the setup actually "3 steps or less"? If not, document the complexity.
- **Badges**: Add CI status, version, and tech stack badges.

### 2. API Documentation (The Interface)
- **OpenAPI/Swagger**: For REST APIs. Ensure request/response schemas are accurate.
- **PHPDoc / JSDoc**: Ensure complex functions have parameter and return type hints.
- **Examples**: Every endpoint MUST have a curl or JSON example.

### 3. Architecture Decision Records (ADR)
- **When**: Whenever a major tech choice is made (e.g., "Choosing Postgres over MySQL", "Using TanStack Query").
- **Format**:
    - **Context**: What was the problem?
    - **Decision**: What did we choose?
    - **Consequences**: What are the trade-offs (positive and negative)?

### 4. Change Log
- **Keep a CHANGELOG.md**: Follow "Keep a Changelog" format (Added, Changed, Deprecated, Removed, Fixed).

## Workflow
1.  **Scan**: Read existing docs.
2.  **Gap Analysis**: What is missing or outdated compared to the code?
3.  **Draft**: Write concise, developer-friendly markdown.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hadimiftahulf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
