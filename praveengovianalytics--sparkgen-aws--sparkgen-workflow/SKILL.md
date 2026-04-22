---
name: sparkgen-workflow
description: View, validate, and edit the ai_workflow.yaml configuration Use when this capability is needed.
metadata:
  author: praveengovianalytics
---

# SparkGen Workflow

Manage the central workflow configuration in `config/ai_workflow.yaml`.

## Dynamic Context

Before any action:
1. Read `config/ai_workflow.yaml` — the single source of truth
2. If server is running, fetch live config: `curl -sf http://localhost:8000/v1/workflow -H "X-API-Key: ${API_KEY:-dev-local-key}"`

## Actions

### Show Workflow (`/sparkgen-workflow show`)
Parse and display `config/ai_workflow.yaml` in a readable format:
- **LLM**: provider, model, region
- **Embedding**: provider, model
- **Agents**: name, role, tools (table format)
- **Orchestration**: pattern, entry_agent, max_iterations
- **Handoffs**: from → to (condition) for each rule
- **Guardrails**: enabled, active sets
- **RAG**: enabled, default mode, knowledge bases
- **Environment overrides**: list which environments have overrides

### Validate Workflow (`/sparkgen-workflow validate`)
```bash
python -c "from app.config.workflow_loader import load_workflow; w = load_workflow(); print(f'Workflow loaded: {w[\"name\"]}, {len(w.get(\"agents\", []))} agents')"
```
Check for:
- YAML syntax errors
- Missing required fields
- Agent references in handoffs match defined agents
- Tool references in agents match defined tools
- Prompt files referenced actually exist

### Add Handoff (`/sparkgen-workflow add-handoff <from> <to> <condition>`)
Add a new routing rule to the `handoffs:` section:
```yaml
- from: <from_agent>
  to: <to_agent>
  condition: "<condition_expression>"
```
Then validate the workflow.

### Set Pattern (`/sparkgen-workflow set-pattern <single_agent|router_manager>`)
Update `orchestration.pattern` in the workflow YAML:
- `single_agent`: Only one agent, no routing
- `router_manager`: Main agent routes to specialists based on intent
Validate that the pattern matches the agent count (single_agent requires 1 agent).

### Environment Override (`/sparkgen-workflow env-override <env_name> <section> <key> <value>`)
Add or update an environment-specific override in the `environments:` section:
```yaml
environments:
  <env_name>:
    <section>:
      <key>: <value>
```

### Edit (`/sparkgen-workflow edit`)
Open `config/ai_workflow.yaml` for direct editing. After changes, always run validate.

## Workflow Structure Reference
The YAML has 13 sections: version, name, description, llm, embedding, knowledge_bases, rag, tools, memory, guardrails, prompts, agents, orchestration, handoffs, observability, evaluation, environments.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/praveengovianalytics) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
