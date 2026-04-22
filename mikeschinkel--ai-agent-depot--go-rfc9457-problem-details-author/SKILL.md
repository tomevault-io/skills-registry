---
name: go-rfc9457-problem-details-author
description: Author and refactor RFC 9457 Problem Details responses using Mike's go-rfc9457 package. Use when designing API error responses, mapping errors to HTTP status, or returning structured problem details. Use when this capability is needed.
metadata:
  author: mikeschinkel
---

# RFC 9457 Problem Details Author

Use this skill when the user asks to:
- return API errors over HTTP (or similar) in a structured way
- define a problem-details schema/payload
- map internal errors to HTTP status codes
- standardize error responses across handlers/services

## Mandatory references (read in order)
1) `references/nonnegotiables.md`
2) `references/doterr.md`
3) `references/clearpath.md` (production code only)
4) `references/go-dt-paths.md` (only if paths/files appear in metadata)
5) `references/rfc9457-package-notes.md` (go-rfc9457 catalog notes)

## Core requirements
- Prefer `go-rfc9457` for Problem Details instead of ad-hoc maps/structs.
- Do not leak internal error strings; use stable **sentinels** + metadata.
- Map known sentinel errors to stable `type` identifiers and HTTP status codes.
- Include only safe details in responses (no secrets, tokens, internal stack traces).

## Design guidance (default)
When designing problem details:
- Define a small set of stable problem `type` values (URIs or uri-like strings).
- Use `title` as a short, stable human label; use `detail` for request-specific explanation (safe).
- Use `instance` when you have a request id / trace id / correlation id.
- Add extensions/metadata keys for machine-consumable specifics (validated fields, limits, etc.).

## What to deliver
When implementing:
- Provide the Problem Details builder/mapping code (often a function that accepts `error` and request context).
- Provide handler examples showing how to return it consistently.
- Provide tests if requested (tests follow `references/testing.md` rules).

## Self-check before final output
- No compound `if init; cond {}` forms.
- No ignored errors.
- Uses doterr for internal errors and go-rfc9457 for external representation.
- Problem response does not expose sensitive/internal information.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mikeschinkel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
