---
name: mcp-management-workflow
description: Complete development workflow for MCP Management System v2.1 including Phase 0 cognitive analysis, MCP service selection and testing, template modification procedures, and deployment protocols. Use when working on MCP Management System development tasks. Use when this capability is needed.
metadata:
  author: dongho5270
---

# MCP Management Workflow

MCP Management System v2.1 개발 워크플로우.

## Development Cycle

| Phase | Purpose | Skills/MCP |
|-------|---------|------------|
| **P0** | Analysis | phase0-analysis |
| **P1** | MCP Selection | mcp-selector |
| **P2** | Implementation | TDD, coding-standards |
| **P3** | Testing | test scripts |
| **P4** | Documentation | CLAUDE.md |
| **P5** | Deployment | deployment-checklist |

## Common Workflows

| Workflow | Steps |
|----------|-------|
| **Quick Bug Fix** | Fix → Test → Commit (fix:) |
| **Feature Addition** | P0 → P1 → P2 → P3 → P4 → P5 |
| **Template Mod** | P0 → Modify → Test tokens → Deploy |
| **MCP Addition** | P0 → Implement → .mcp.json → Test → Deploy |

## Key Commands

```bash
node test-all-15-services.cjs     # Test all MCP
node mcp-bridge.cjs [svc] [method] # Indirect call
gh pr create --title "[T]"         # Create PR
```

## Session Management

**Start**: Read NEXT_SESSION.md → Skills auto-load → Plan 2-3 hours

**End**: Update NEXT_SESSION.md (state, decisions, actions) → Commit WIP

**DON'T document**: Methodologies (in skills), Workflows (in skills), Checklists (in skills)

---

**Detailed workflow guide**: See `prompt.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dongho5270) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
