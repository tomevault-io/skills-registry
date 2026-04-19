---
name: call-foundry-agent
description: Call Foundry agent with a prompt and get the response. Use when this capability is needed.
metadata:
  author: jacwu
---

# Call Azure Foundry Agent

Send a prompt to an Azure Foundry Agent and retrieve the response.

### Parameters

- Required: `prompt` (The input text to send to the agent)
- Optional: 
    - `--agent-name` (The name of the agent to call. Defaults to `AZURE_AI_AGENT_NAME` env var)
    - `--endpoint` (The Azure AI Project Endpoint. Defaults to `AZURE_AI_PROJECT_ENDPOINT` env var)

### Example

```bash
.venv/Scripts/python .github/skills/call_foundry_agent/scripts/call_foundry.py "search the latest news in 2026"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jacwu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
