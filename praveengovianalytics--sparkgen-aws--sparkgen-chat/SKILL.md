---
name: sparkgen-chat
description: Send a chat message to the running agent server and display the response Use when this capability is needed.
metadata:
  author: praveengovianalytics
---

# SparkGen Chat

Send a chat message to the running SparkGen agent and display the full response.

## Dynamic Context

Before sending:
1. Check server health: `curl -sf http://localhost:8000/health`
   - If not running: suggest `/sparkgen-deploy local` or `make local`
2. Get API key from `.env` (default: `dev-local-key`)

## Actions

### Send Chat Message
Build and send the request:
```bash
curl -s -X POST http://localhost:8000/v1/chat \
  -H "Content-Type: application/json" \
  -H "X-API-Key: ${API_KEY:-dev-local-key}" \
  -d '{
    "message": "<user message>",
    "agent_name": "<agent or empty for default>",
    "session_id": "<session or auto-generated>",
    "max_iterations": <N or 10>
  }'
```

### Display Response
Parse and display the JSON response:
- **Response text**: the agent's reply
- **Agent used**: which agent handled the request
- **Tool calls**: list any tools that were invoked (name + args + result summary)
- **Iterations**: how many LLM calls were made
- **Latency**: response time
- **Session ID**: for follow-up messages

### Multi-Turn Conversation
When `--session` is provided, the agent maintains conversation history. Use the same session ID for follow-up messages to test multi-turn behavior.

## Examples
```
/sparkgen-chat "What tools do you have?"
/sparkgen-chat "Search for deployment docs" --agent researcher
/sparkgen-chat "Follow up on that" --session abc123
```

## Error Handling
- Server not running → suggest deployment
- 401 Unauthorized → check API_KEY in .env
- 500 error → show error detail, suggest `/sparkgen-debug general`
- Timeout → check Ollama is responding (`curl http://localhost:11434/api/tags`)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/praveengovianalytics) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
