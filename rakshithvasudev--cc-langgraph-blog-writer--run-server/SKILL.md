---
name: run-server
description: Start the LangGraph research agent development server Use when this capability is needed.
metadata:
  author: rakshithvasudev
---

# Run Server

Start the LangGraph development server for the research agent.

## Instructions

Run the following command to start the server:

```bash
cd /home/rajathdb/research_agent && langgraph dev
```

## Server URLs

Once running, access:

| Service | URL |
|---------|-----|
| API | http://127.0.0.1:2024 |
| Studio UI | https://smith.langchain.com/studio/?baseUrl=http://127.0.0.1:2024 |
| API Docs | http://127.0.0.1:2024/docs |

## Quick Test

To test with a new research topic in Studio:

1. Open the Studio UI link above
2. Create a **new thread** (important!)
3. Submit input:

```json
{
  "topic": "Your research topic",
  "review_mode": "autonomous",
  "max_iterations": 5
}
```

## Stop Server

Press `Ctrl+C` in the terminal to stop the server.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rakshithvasudev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
