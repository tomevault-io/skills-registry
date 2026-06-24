---
name: langchain-agent-architecture-review
description: Use when reviewing LangChain agent architecture, chain composition, tool integration, memory, retrieval, callbacks, and production readiness.
metadata:
  author: WangYao-GoGoGo
---

# LangChain Agent Architecture Review

## When To Use

- The main decision is about LangChain chain/agent structure, tool definitions, memory configuration, or retrieval pipeline.
- Reviewing prompt engineering, callback boundaries, or evaluation setup.

## Workflow

1. Identify the chain/agent topology — linear chains, LCEL pipelines, or agent loops.
2. Review tool definitions for side-effect boundaries, idempotency, and credential isolation.
3. Check memory configuration — is it scoped per session, per user, or global?
4. Review retrieval setup — vector store, chunking strategy, retriever configuration.
5. Check callback and tracing setup for observability.
6. Review prompt templates for injection risks and output structure.
7. Recommend the smallest structural change that improves safety or clarity.

## Output Format

```markdown
LangChain architecture review:
- Chain/agent topology:
- Tool definitions & side effects:
- Memory configuration:
- Retrieval pipeline:
- Callbacks & tracing:
- Prompt safety:
- Recommended change:
- Verification:
```

---
> Source: [WangYao-GoGoGo/architecture-agent-skills](https://github.com/WangYao-GoGoGo/architecture-agent-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
