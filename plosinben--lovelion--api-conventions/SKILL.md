---
name: api-conventions
description: API design conventions Use when this capability is needed.
metadata:
  author: plosinben
---

# API Conventions Mandate

## URL & Methods
- **Resource Names**: Plural nouns (e.g., `/spaces`, `/transactions`).
- **Style**: kebab-case for multi-word (e.g., `/space-members`).
- **Nesting**: Parent -> Child (e.g., `/spaces/{id}/transactions`).
- **GET**: 200 OK.
- **POST**: 201 Created. Return resource directly.
- **PUT/PATCH/DELETE**: 200 OK.

## Request/Response Format
- **Success**: Return resource directly (No wrapper).
- **Error**: MUST be `{"error": "message"}`.
- **IDs**:
  - NanoID: Publicly visible (spaces, transactions, invites).
  - UUID: Internal linkage (users, space_members, items).

## Handler Structure
- **Transactions**: Mandatory for multi-table ops.
- **Validation**: MUST use Gin binding (`required`, `min=1`).
- **Verification**: ALWAYS verify ownership/access BEFORE processing.
- **ID Gen**: Use `utils.NewShortID` for public entities.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/plosinben) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
