---
name: sparkgen-test
description: Run tests — unit, docker, guardrails, prompts, rag-eval, smoke, validate, or all Use when this capability is needed.
metadata:
  author: praveengovianalytics
---

# SparkGen Test

Run project tests. Default is `unit` if no argument given.

## Dynamic Context

Before running tests:
1. List test files: `ls tests/`
2. Check current deployment mode from `.env` if it exists
3. Check if server is running (needed for smoke/docker tests): `curl -sf http://localhost:8000/health`

## Test Types

### Unit Tests (`/sparkgen-test unit` or `/sparkgen-test`)
```bash
pytest tests/ -v -m "not docker and not aws"
```
Runs all unit tests excluding docker and aws markers. Expects ~124 tests.

### Docker Tests (`/sparkgen-test docker`)
```bash
pytest tests/test_docker_compose.py -v -m docker
```
Requires Docker Compose stack running (`make docker-up` first).

### Guardrail Tests (`/sparkgen-test guardrails`)
```bash
python -m app.guardrails.test_runner --defaults guardrails/default_guardrails.yaml
```
Runs guardrail rule test cases from the YAML definitions.

### Prompt Validation (`/sparkgen-test prompts`)
```bash
python -m app.prompts.validator --base-dir . --variables config/prompt_variables.yaml
```
Validates all prompt templates resolve correctly with defined variables.

### RAG Evaluation (`/sparkgen-test rag-eval`)
```bash
python -m app.rag.eval --kb default --config config/rag.yaml
```
Runs RAG quality evaluation (accuracy, faithfulness, relevancy).

### Smoke Tests (`/sparkgen-test smoke`)
Requires a running server. Run these sequentially:
```bash
curl -sf http://localhost:8000/health
curl -sf http://localhost:8000/v1/agents -H "X-API-Key: ${API_KEY:-dev-local-key}"
curl -sf http://localhost:8000/v1/tools -H "X-API-Key: ${API_KEY:-dev-local-key}"
curl -s -X POST http://localhost:8000/v1/chat -H "Content-Type: application/json" -H "X-API-Key: ${API_KEY:-dev-local-key}" -d '{"message": "Hello"}'
```
Verify each returns 200 with valid JSON.

### Validate All (`/sparkgen-test validate`)
```bash
make validate
```
Runs prompt validation + guardrail tests + workflow config loading.

### All Tests (`/sparkgen-test all`)
Run in sequence: unit → guardrails → prompts → validate. Report summary at end.

## Output
- Show pass/fail count for each test type
- For failures: show the failing test name + error message
- At the end: print a summary table of all test results

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/praveengovianalytics) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
