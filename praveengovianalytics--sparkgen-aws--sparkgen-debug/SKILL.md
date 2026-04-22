---
name: sparkgen-debug
description: Diagnose issues with Ollama, Docker, AWS, endpoints, guardrails, RAG, or general health Use when this capability is needed.
metadata:
  author: praveengovianalytics
---

# SparkGen Debug

Diagnose and troubleshoot issues. Default is `general` which runs all checks.

## Dynamic Context

Always gather this state first:
1. Check server: `curl -sf http://localhost:8000/health`
2. Check Ollama: `curl -sf http://localhost:11434/api/tags`
3. Check Docker: `docker info 2>/dev/null && docker compose ps 2>/dev/null`
4. Read current `.env` if exists for DEPLOYMENT_MODE and LLM_PROVIDER

## Diagnostic Topics

### Ollama (`/sparkgen-debug ollama`)
1. Check Ollama running: `curl -sf http://localhost:11434/api/tags`
2. List models: `ollama list`
3. Verify model matches `.env.local` OLLAMA_MODEL (must include tag, e.g., `llama3.2:3b`)
4. Test model: `curl -s http://localhost:11434/api/generate -d '{"model":"<model>","prompt":"hi","stream":false}'`
5. Check Ollama logs: `cat ~/.ollama/logs/server.log | tail -50` (macOS)

### Docker (`/sparkgen-debug docker`)
1. Check Docker daemon: `docker info`
2. Check running containers: `docker compose ps`
3. Check agent logs: `docker compose logs --tail=50 agent`
4. Check DynamoDB Local: `curl -sf http://localhost:8000/shell/`
5. Check Redis: `redis-cli -h localhost ping`
6. Check port conflicts: `lsof -i :8000 -i :8080 -i :6379 -i :11434`

### AWS (`/sparkgen-debug aws`)
1. Check credentials: `aws sts get-caller-identity`
2. Check region: `aws configure get region`
3. Test Bedrock access: `aws bedrock list-foundation-models --max-results 1`
4. Check DynamoDB tables: `aws dynamodb list-tables`
5. Check S3 buckets: `aws s3 ls | grep <project_prefix>`

### Endpoint (`/sparkgen-debug endpoint`)
1. Health: `GET /health`
2. Agents: `GET /v1/agents`
3. Tools: `GET /v1/tools`
4. Workflow: `GET /v1/workflow`
5. Guardrails: `GET /v1/guardrails`
6. Prompts: `GET /v1/prompts`
7. Chat: `POST /v1/chat` with test message
For each, report status code and whether response is valid JSON.

### Guardrail (`/sparkgen-debug guardrail`)
1. Check guardrails endpoint: `GET /v1/guardrails`
2. Validate rules file: `python -m app.guardrails.test_runner --defaults guardrails/default_guardrails.yaml`
3. Test guardrail: `POST /v1/guardrails/test` with sample input
4. Check for YAML syntax errors in `guardrails/default_guardrails.yaml`

### RAG (`/sparkgen-debug rag`)
1. Check RAG config: read `config/rag.yaml`
2. Check knowledge bases: `GET /v1/rag/knowledge-bases`
3. Check documents directory exists and has files: `ls documents/`
4. Check vector index exists: `ls local_data/vectors/`
5. Test query: `POST /v1/rag/query` with simple question
6. Check embedding provider is available

### General (`/sparkgen-debug general` or `/sparkgen-debug`)
Run ALL checks above. Output a health dashboard:
```
=== SparkGen Health Dashboard ===
Server:      [OK|FAIL] http://localhost:8000
Ollama:      [OK|FAIL|N/A] http://localhost:11434
Docker:      [OK|FAIL|N/A]
Env File:    [OK|MISSING] .env → DEPLOYMENT_MODE=<mode>
Endpoints:   [OK|FAIL] <N>/7 responding
Guardrails:  [OK|FAIL|DISABLED]
RAG:         [OK|FAIL|DISABLED]
Tests:       <last run status if available>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/praveengovianalytics) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
