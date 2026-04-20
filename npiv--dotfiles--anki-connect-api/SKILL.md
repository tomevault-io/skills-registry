---
name: anki-connect-api
description: Use when working with Anki-Connect's HTTP API to query decks/cards, move cards between decks, or manage media files by sending JSON requests to a local Anki instance on port 8765, including handling API versioning and optional authentication.
metadata:
  author: npiv
---

# Anki-Connect API

## Overview

Use this skill to build or troubleshoot Anki-Connect API calls for local Anki automation. It focuses on request/response shape, versioning, authentication, and the action-specific payloads for cards, decks, and media.

## Workflow

1. Confirm Anki is running and AnkiConnect is installed (HTTP server on port 8765).
2. Decide whether an API key is required and include `key` if configured.
3. Send a POST request with `action`, `version`, and `params`; always set `version` to 6 to keep the `error` field in responses.
4. Use the relevant action reference file for the exact params and examples.

## Action references

Read only the specific reference file needed to avoid loading the entire API documentation:

- Request/response format, authentication, and sample client code: `skills/anki-connect-api/references/overview.md`
- Card-related actions (find cards, card info, intervals): `skills/anki-connect-api/references/card-actions.md`
- Deck-related actions (list/create/move): `skills/anki-connect-api/references/deck-actions.md`
- Media actions (store/retrieve/delete media files): `skills/anki-connect-api/references/media-actions.md`

## Quick example

```json
{
    "action": "deckNames",
    "version": 6
}
```

POST to `http://127.0.0.1:8765` and read `result` or `error` from the response.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/npiv) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
