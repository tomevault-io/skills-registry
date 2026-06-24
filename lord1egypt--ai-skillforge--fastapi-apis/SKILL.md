---
name: fastapi-apis
description: Building high-performance, asynchronous REST APIs with FastAPI, Pydantic, and automatic OpenAPI validation. Use when this capability is needed.
metadata:
  author: Lord1Egypt
---

# Fastapi Apis

## Overview
FastAPI is a modern, fast (high-performance) web framework for building APIs with Python 3.8+ based on standard Python type hints.

## When to Use This Skill
Use when creating RESTful web backends, microservices, or custom AI agent action gateways.

## Quick Start (with runnable code examples)

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI(title="Agent API Gateway")

class QueryPayload(BaseModel):
    prompt: str
    max_tokens: int = 100

@app.post("/query")
async def handle_query(payload: QueryPayload):
    return {"status": "ok", "echo": payload.prompt}
```

## Advanced Usage
Integrate custom exception handlers, dependency injection overrides, and background task queues.

## Key References
- [FastAPI Documentation](https://fastapi.tiangolo.com/)

## Dependencies
- fastapi>=0.100.0, uvicorn>=0.20.0

---
> Source: [Lord1Egypt/ai-skillforge](https://github.com/Lord1Egypt/ai-skillforge) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
