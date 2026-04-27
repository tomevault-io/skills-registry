---
name: api-error-taxonomy
description: Define consistent API error codes and responses. Use when a mid-level developer needs error standardization. Use when this capability is needed.
metadata:
  author: proflead
---

# API Error Taxonomy

## Purpose
Define consistent API error codes and responses.

## Inputs to request
- Current error responses and status usage.
- Client expectations for retries and messaging.
- Logging and observability requirements.

## Workflow
1. List common error categories and HTTP mappings.
2. Define error payload shape and fields.
3. Set guidelines for logging and client messaging.

## Output
- Error taxonomy table and examples.

## Quality bar
- Ensure errors are machine-parseable.
- Align retryable vs non-retryable semantics.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/proflead) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
