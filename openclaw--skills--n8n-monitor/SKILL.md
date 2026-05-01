---
name: n8n-monitor
description: Monitoramento operacional do N8N via Docker. Use when this capability is needed.
metadata:
  author: openclaw
---
# Skill: n8n-monitor

Monitoramento operacional do N8N via Docker.

## Capabilities
- Verificar status dos containers N8N
- Ler logs recentes
- Checar saúde do container
- Analisar uso de CPU e memória

## Commands
- docker ps | grep n8n
- docker logs --tail 50 n8n
- docker inspect --format='{{.State.Health.Status}}' n8n
- docker stats --no-stream n8n

## Output
Respostas em Markdown, com tabelas simples e status claro.

## Status
active

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
