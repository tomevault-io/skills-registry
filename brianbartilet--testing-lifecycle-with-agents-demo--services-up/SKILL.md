---
name: services-up
description: Start Docker Compose services for local development and testing. Pass service names as arguments (e.g. "todo-backend jira-mock") or leave blank to start all services. Use when this capability is needed.
metadata:
  author: brianbartilet
---

Start services. If `$ARGUMENTS` is provided, start only those services; otherwise start all.

!`docker compose up -d ${ARGUMENTS:-todo-backend todo-frontend jira-mock} 2>&1`

Then wait for the backend to be healthy:

!`for i in $(seq 1 15); do curl -sf http://localhost:8000/health && echo "backend ready" && break || sleep 2; done 2>&1`

Report:
- Which containers are now running (`docker compose ps`)
- Health check result for the backend
- If any container failed to start, show its logs

---
> Source: [brianbartilet/testing-lifecycle-with-agents-demo](https://github.com/brianbartilet/testing-lifecycle-with-agents-demo) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
