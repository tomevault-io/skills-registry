---
name: workflowrespond-to-approval
description: Records a response to an approval request. Use when this capability is needed.
metadata:
  author: venikman
---

# Workflow: Respond to Approval

## 1. Context

This skill records an approval decision linked to a prior approval request for auditability and gating.

## 2. Inputs

- **context** (required): Bounded context name (safe path segment).
- **session_id** (required): Session identifier (safe path segment).
- **approval_id** (required): Approval identifier (safe path segment).
- **decision** (required): Approval decision (`approved`, `denied`, `needs-review`).
- **responded_by** (optional): Responder label.
- **notes** (optional): Response notes or conditions.
- **agent_model** (optional): Agent model identifier.
- **role_assignment** (optional): RoleAssignment for U.Work logging (default: Strategist).
- **decisions** (optional): Semicolon-delimited DRR ids/paths.
- **timestamp_start** (optional): ISO-8601 timestamp for deterministic output.

## 3. Outputs

- `runtime/contexts/<context>/approvals/<session_id>.<approval_id>.<timestamp>.response.md`

## 4. Procedure

1. Validate inputs and confirm the approval request exists.
2. Write the approval response record with the decision.
3. Emit U.Work via `telemetry/log-work` when available.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/venikman) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
