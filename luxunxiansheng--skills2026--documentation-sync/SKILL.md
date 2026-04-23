---
name: documentation-sync
description: Use when major refactors or feature additions may have changed behavior, APIs, setup, or plans and documentation needs to be validated against the current repo.
metadata:
  author: luxunxiansheng
---

# Documentation Sync

## Intent
Automatically triggered or manually requested to verify that the `README.md`, `docs/`, and `OPENAPI` specs match the current codebase.

## Checklist
1. **README Consistency**: Check if the "Getting Started" or "Architecture" sections in `README.md` are still accurate after recent changes.
2. **API Alignment**: Verify that server routes in `apps/agent_server/src/main.py` match descriptions in docs (for example `docs/` and `README.md`).
3. **Plan Updates**: If a task in `project/plans/` is completed, ensure implementation details are reflected in final documentation.
4. **Environment Variables**: Check if new `.env` variables have been added to `README.md` or `.env.example`.

## Output Format
### Documentation Status
- **Files Checked**: list of files
- **Sync Issues**:
  - [File]: [What is missing or outdated]
- **Suggested Updates**:
  - [Snippet of updated documentation]

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luxunxiansheng) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
