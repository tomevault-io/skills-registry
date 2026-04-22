---
name: sparkgen-deploy
description: Deploy SparkGen agent to local, docker, or aws mode Use when this capability is needed.
metadata:
  author: praveengovianalytics
---

# SparkGen Deploy

Deploy the agent server in the specified mode.

## Dynamic Context

Before deploying, gather current state:
1. Read `.env` (if exists) to check current DEPLOYMENT_MODE and LLM_PROVIDER
2. Check if server is already running: `curl -sf http://localhost:8000/health`
3. For docker mode: check Docker is running with `docker info`
4. For local mode: check Ollama with `curl -sf http://localhost:11434/api/tags`

## Deployment Modes

### Local (`/sparkgen-deploy local`)
1. Verify Ollama is running: `curl -sf http://localhost:11434/api/tags`
   - If not: instruct user to run `ollama serve` and `ollama pull llama3.2:3b`
2. Source environment: `set -a && source .env.local && set +a`
3. Start server: `uvicorn app.api:app --host 0.0.0.0 --port 8000 --reload`
4. Verify: `curl -sf http://localhost:8000/health`
5. Print summary: endpoint URL, provider, model, available agents

### Docker (`/sparkgen-deploy docker`)
1. Verify Docker is running: `docker info`
2. Copy env file: `cp .env.docker .env`
3. Start stack: `docker compose up --build -d`
4. Wait for health: poll `curl -sf http://localhost:8080/health` (up to 60s)
5. Check all services: `docker compose ps`
6. Print summary: agent URL (:8080), DynamoDB Local (:8000), Redis (:6379)

### AWS (`/sparkgen-deploy aws [--target eks|lambda]`)
1. Verify AWS credentials: `aws sts get-caller-identity`
2. Check `.env.aws` exists and has required vars (AWS_REGION, AWS_MODEL_ID)
3. For EKS: run `bash scripts/deploy.sh eks`
4. For Lambda: run `bash scripts/deploy.sh lambda`
5. Verify deployment health via API Gateway URL from `.env.aws`

## Error Handling
- If port 8000 is already in use: suggest `lsof -i :8000` to find the process
- If Ollama model not found: run `ollama pull <model>`
- If Docker build fails: show `docker compose logs` output

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/praveengovianalytics) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
