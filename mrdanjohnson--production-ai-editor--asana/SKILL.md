---
name: asana
description: Access Asana task management via the Asana API. Use when the user needs to check tasks, create tasks, search projects, or manage Asana workflows. Requires a Personal Access Token from Asana. Use when this capability is needed.
metadata:
  author: mrdanjohnson
---

# Asana Skill

Access Asana project management through the API.

## Setup Required

### 1. Get Personal Access Token

1. Go to https://app.asana.com/0/developer-console
2. Click "Create new personal access token"
3. Name it: "OpenClaw Integration"
4. Copy the token (looks like `1/1234567890:...`)

### 2. Store the Token

```bash
echo "YOUR_TOKEN_HERE" > ~/.config/asana/token
```

### 3. Test Connection

```bash
~/.openclaw/workspace/scripts/asana.sh me
```

## Common Operations

### Get User Info
```bash
~/.openclaw/workspace/scripts/asana.sh me
```

### List Workspaces
```bash
~/.openclaw/workspace/scripts/asana.sh workspaces
```

### List Projects
```bash
# All projects
~/.openclaw/workspace/scripts/asana.sh projects

# Projects in specific workspace
~/.openclaw/workspace/scripts/asana.sh projects <workspace_id>
```

### Work with Tasks

**List tasks in a project:**
```bash
~/.openclaw/workspace/scripts/asana.sh tasks list <project_id>
```

**Get task details:**
```bash
~/.openclaw/workspace/scripts/asana.sh tasks get <task_id>
```

**Create a task:**
```bash
~/.openclaw/workspace/scripts/asana.sh tasks create <project_id> "Task Name" "Description"
```

**Search tasks:**
```bash
~/.openclaw/workspace/scripts/asana.sh search <workspace_id> "query"
```

## Finding IDs

Asana uses numeric IDs. To find them:

1. **Workspace ID:** From URL `https://app.asana.com/0/1234567890/...` → `1234567890`
2. **Project ID:** From URL `https://app.asana.com/0/0/9876543210` → `9876543210`
3. **Task ID:** From URL `https://app.asana.com/0/0/111222333` → `111222333`

## Automation Ideas

### Weekly Task Summary
Pull all incomplete tasks and summarize for review.

### Email-to-Task
Create Asana tasks from specific emails (e.g., action items from Church Production).

### Task Notifications
Check for tasks due soon and alert via preferred channel.

### Project Health
Track task completion rates, identify stalled projects.

## Integration with Other Workflows

### Church Production → Asana
When Church Production emails contain action items:
1. Parse the email
2. Extract action items
3. Create Asana tasks
4. Link back to original article

Example workflow:
```
Read email about Yamaha trade-up → Create task: "Evaluate Yamaha upgrade before March 6"
Read leadership article → Create task: "Discuss delegation strategies with team"
```

### Calendar → Asana
Create tasks for upcoming events that need production planning.

## Security

- Never commit the token to git
- Token is stored in `~/.config/asana/token`
- Token has same permissions as your Asana account
- To revoke: Delete token in Asana developer console

## API Limits

- 1500 requests per minute per user
- Rate limit headers included in responses
- This wrapper uses minimal API calls

## Error Handling

Common errors:
- `401 Unauthorized` → Token expired or invalid, re-authenticate
- `403 Forbidden` → Token doesn't have permission for that resource
- `404 Not Found` → ID doesn't exist or you don't have access
- `429 Too Many Requests` → Rate limited, wait and retry

## References

- [Asana API Docs](https://developers.asana.com/)
- [Personal Access Token](https://developers.asana.com/docs/personal-access-token)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mrdanjohnson) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
