---
name: gitlab
description: Integrate with GitLab for DevOps. Use when you need to: (1) create and manage GitLab issues, (2) trigger CI/CD pipelines, or (3) automate development workflows. Use when this capability is needed.
metadata:
  author: refly-ai
---

# Gitlab

Integrate with GitLab for DevOps. Use when you need to: (1) create and manage GitLab issues, (2) trigger CI/CD pipelines, or (3) automate development workflows.

## Input

Provide input as JSON:

```json
{
  "project_id": "GitLab project ID or path (e.g., 'username/project-name' or numeric ID)",
  "issue_title": "Title for the new issue to be created",
  "issue_description": "Detailed description of the issue",
  "issue_labels": "Comma-separated labels for the issue (e.g., 'bug,urgent,backend')",
  "pipeline_ref": "Git reference (branch or tag) to check pipeline status for (e.g., 'main', 'develop')"
}
```

## Execution (Pattern C: Action)

### Step 1: Run the Skill and Get Run ID

```bash
RESULT=$(refly skill run --id skpi-f72iylyg8wty6ruhnaimunzs --input '{
  "project_id": "12345",
  "action": "list_issues",
  "state": "opened"
}')
RUN_ID=$(echo "$RESULT" | jq -r '.payload.workflowExecutions[0].id')
# RUN_ID is we-xxx format, use this for workflow commands
```

### Step 2: Open Workflow in Browser and Wait for Completion

```bash
open "https://refly.ai/workflow/c-vxihzm2o13l5da2dxjaqw4ki"
refly workflow status "$RUN_ID" --watch --interval 30000
```

### Step 3: Confirm Action Status

```bash
# Confirm action completed
STATUS=$(refly workflow detail "$RUN_ID" | jq -r '.payload.status')
echo "Action completed with status: $STATUS"
```

## Expected Output

- **Type**: API Response
- **Format**: JSON repository/issue data
- **Action**: Confirm issue created or pipeline triggered

## Rules

Follow base skill workflow: `~/.claude/skills/refly/SKILL.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/refly-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
