---
name: ai-system-architecture
description: Design AI-powered systems, automation workflows, APIs, and agents. Use when architecting integrations, optimizing costs, building pipelines, or designing agentic systems. Prioritizes reusability, cost-efficiency, and minimal human operation. Use when this capability is needed.
metadata:
  author: aesthetic-inc
---

# AI System Architecture

Design automation systems, APIs, and AI agents with production-grade reliability.

## Architecture Workflow

1. **Goal** → Define outcome and success metrics
2. **Constraints** → Budget, latency, reliability, integrations
3. **Components** → Identify required building blocks
4. **Design** → Architecture diagram and data flow
5. **Optimize** → Cost, latency, failure modes
6. **Implement** → Phased rollout plan
7. **Operate** → Monitoring, alerting, maintenance

## LLM Cost Optimization

| Strategy | When to Use | Savings |
|----------|-------------|---------|
| Model tiering | Route simple tasks to cheaper models | 50-80% |
| Caching | Repeated identical queries | 90%+ |
| Batching | Non-realtime processing | 20-40% |
| Prompt compression | Long context tasks | 30-50% |
| Output length control | Verbose responses | 20-30% |

## Agent Design Principles

1. **Single responsibility** → One agent, one job
2. **Explicit handoffs** → Clear triggers between agents
3. **Fail gracefully** → Define fallback for every failure mode
4. **Human escalation** → Always have escape hatch
5. **Audit trail** → Log decisions, not just outputs

## Reusability Standards

1. **Configuration over code** → Parameters externalized
2. **Modular components** → Swap without rewrite
3. **Documentation** → Inline comments + README
4. **Testing** → Unit tests for core logic
5. **Versioning** → Semantic versioning for APIs

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aesthetic-inc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
