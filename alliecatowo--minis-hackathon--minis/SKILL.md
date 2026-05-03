---
name: minis
description: | Use when this capability is needed.
metadata:
  author: alliecatowo
---

# Minis - Developer Personality Clones in Your Terminal

Minis creates AI personality clones from GitHub profiles. Each mini captures a developer's
coding values, communication style, and personality patterns from their public GitHub activity.

## Minis API Reference

The Minis backend runs at `http://localhost:8000`. All endpoints are under `/api/`.

### Create a Mini

```
POST http://localhost:8000/api/minis
Content-Type: application/json
{"username": "<github_username>"}
```

Returns 202 with a `MiniSummary`. The pipeline runs in the background. Status values:
`processing`, `ready`, `failed`, `pending`.

### List All Minis

```
GET http://localhost:8000/api/minis
```

Returns an array of `MiniSummary` objects.

### Get Mini Details

```
GET http://localhost:8000/api/minis/<username>
```

Returns a `MiniDetail` with fields: `username`, `display_name`, `avatar_url`, `bio`,
`spirit_content`, `system_prompt`, `values_json`, `metadata_json`, `status`.

### Chat with a Mini

```
POST http://localhost:8000/api/minis/<username>/chat
Content-Type: application/json
{"message": "...", "history": [{"role": "user", "content": "..."}, {"role": "assistant", "content": "..."}]}
```

Returns a Server-Sent Events (SSE) stream with `event: chunk` containing text deltas
and `event: done` when complete.

### Pipeline Status Stream

```
GET http://localhost:8000/api/minis/<username>/status
```

SSE stream of pipeline progress events during mini creation.

### Health Check

```
GET http://localhost:8000/api/health
```

## Working with Minis

### Fetching Mini Personality

To use a mini's personality for code review or conversation, first fetch the mini details:

```bash
curl -s http://localhost:8000/api/minis/<username> | jq .
```

The `system_prompt` field contains the full personality prompt. The `values_json` field
contains structured engineering values, communication style, and personality patterns.

### Collecting Chat Responses from SSE

The chat endpoint returns SSE. To collect the full response:

```bash
curl -s -N -X POST http://localhost:8000/api/minis/<username>/chat \
  -H "Content-Type: application/json" \
  -d '{"message": "...", "history": []}' | \
  grep '^data: ' | sed 's/^data: //' | grep -v '^$' | tr -d '\n'
```

### Error Handling

- **404**: Mini not found. Suggest creating it with `/mini-create`.
- **409**: Mini is not ready yet (still processing). Check status.
- **Connection refused**: Backend is not running. Start with `mise run dev-backend`
  or `cd backend && uv run uvicorn app.main:app --reload --port 8000`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alliecatowo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
