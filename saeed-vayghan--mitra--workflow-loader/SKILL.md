---
name: workflow-loader
description: Provides robust mechanisms for agents to dynamically load and execute markdown workflows.
metadata:
  author: saeed-vayghan
---

# Workflow Loader Skill

This skill provides the capability for any AntiGravity Agent to robustly load, read, and execute external workflow files defined in the `mitra/agents/{agent}/workflows/` directory.

## 🛑 The Problem
By default, an instruction like "Action: Load backend.md" is ambiguous. The LLM might:
1.  **Hallucinate**: Pretend it read the file and invent steps.
2.  **Fail**: Not know where the file is located.
3.  **Guess**: Use general knowledge instead of your specific process.

## ✅ The Solution
This skill provides strict **Tools** (Python scripts) that you MUST use when a workflow is requested.

## 🛠️ Usage Instructions

### 1. Listing Available Workflows
When a user asks "What can you do?" or you need to see available modules for an agent:

Run:
```bash
./.agent/skills/workflow-loader/scripts/list_workflows.sh --agent {agent_name}
```
*Example: `./.agent/skills/workflow-loader/scripts/list_workflows.sh --agent architect`*

### 2. Loading a Workflow
When you trigger a menu handler (e.g., `*backend`) or need to execute a specific process:

Run:
```bash
./.agent/skills/workflow-loader/scripts/load_workflow.sh --agent {agent_name} --workflow {workflow_name}
```
*Example: `./.agent/skills/workflow-loader/scripts/load_workflow.sh --agent architect --workflow backend`*

### 3. Execution Protocol
Once the content is loaded (printed to your context):
1.  **Acknowledge**: "Workflow loaded: [Title]"
2.  **Step-by-Step**: Execute the instructions in the markdown file sequentially.
3.  **Context**: Treat the loaded content as the "Active Protocol" for the current turn.

## ⚠️ Critical Rules
- **NEVER** pretend to load a file. If you haven't run the `load_workflow.sh` script, you haven't loaded it.
- **ALWAYS** check the output of the script. If it says "File not found", inform the user.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/saeed-vayghan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
