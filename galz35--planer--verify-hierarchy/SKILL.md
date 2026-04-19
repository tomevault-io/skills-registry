---
name: verify-hierarchy
description: Validates that the codebase complies with strict hierarchy-based permission rules and does not use hardcoded cargo names.
metadata:
  author: galz35
---

# Verify Hierarchy Compliance

This skill helps ensure that the application follows the strict security and hierarchy rules defined for the project.

## 🚫 Prohibited Patterns
The agent MUST flag and ask the user to fix any code that uses the following patterns for permission logic:
- hardcoded cargo strings (e.g., `user.cargo === 'Lider'`, `user.cargo === 'Gerente'`)
- arbitrary role strings not present in the `rolGlobal` enum (e.g., `user.role === 'Supervisor'`)
- Manual toggles for permissions (e.g., `isManagerMode` state without proper backing logic)

## ✅ Required Patterns
Permission logic MUST be based on:
1. **Global Roles**: `user.rolGlobal === 'Admin'` or `user.rolGlobal === 'Jefe'`
2. **Organizational Hierarchy**: Comparing `user.idOrg` with the target resource's `idNodo` or `idOrg`.
   - Example: `const isManager = user.rolGlobal === 'Jefe' && user.idOrg === project.idNodo;`

## Instructions for the Agent
When asked to review code or implement features:
1. **Scan** for prohibited patterns first.
2. **Refactor** logic to use `rolGlobal` and `idOrg`.
3. **Verify** that `Admin` users always have bypass access.
4. If a specific exception is needed, **instruct** the user to use the `p_permiso_empleado` table logic instead of hardcoding.

## Reference
See `d:\planificacion\.proyecto\documentacion_tecnica\REGLAS_PERMISOS.md` for full documentation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/galz35) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
