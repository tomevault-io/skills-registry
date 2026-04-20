---
name: mcp-linear
description: Call Linear MCP tools via mcporter CLI \u2014 issues, projects, teams, comments, cycles, milestones, and more. Use for: 'create Linear issue', 'list my issues', 'update issue status', 'check sprint progress', 'add comment to ticket'. Replaces native Linear MCP plugin to save context window. Use when this capability is needed.
metadata:
  author: helloworldsungin
---

<objective>
Call Linear MCP tools through the mcporter CLI instead of loading 33 tool definitions into the context window. This skill covers all Linear operations: issues, projects, teams, comments, documents, cycles, milestones, labels, attachments, and users.
</objective>

<process>

## Call Syntax

Two equivalent forms — use whichever is clearer:

```bash
# Colon-delimited (shell-friendly, best for simple args)
mcporter call linear.TOOL_NAME key:value key2:value2 --output json

# Function-call style (better for complex args)
mcporter call 'linear.TOOL_NAME(key: "value", key2: "value2")' --output json
```

Always use `--output json` for machine-readable results you'll parse.

## Issues

```bash
# List issues (assignee "me" = current user)
mcporter call linear.list_issues assignee:me limit:10 --output json

# Search issues by text
mcporter call linear.list_issues query:"search terms" team:ENG --output json

# Get single issue by ID
mcporter call linear.get_issue id:ENG-123 --output json

# Get issue with relations (blocking/blocked-by/duplicates)
mcporter call linear.get_issue id:ENG-123 includeRelations:true --output json

# Create issue
mcporter call 'linear.create_issue(title: "Bug: login broken", team: "ENG", priority: 2, assignee: "me")' --output json

# Create issue with labels
mcporter call 'linear.create_issue(title: "Fix deploy", team: "ENG", labels: "[\"Bug\",\"Infra\"]")' --output json

# Update issue (state, priority, assignee, etc.)
mcporter call 'linear.update_issue(id: "ENG-123", state: "In Progress", priority: 1)' --output json

# Filter by state, label, project, priority, delegate
mcporter call linear.list_issues state:started label:bug project:infra priority:2 --output json

# Filter by date (ISO-8601 durations)
mcporter call 'linear.list_issues(team: "ENG", createdAt: "-P7D")' --output json
mcporter call 'linear.list_issues(team: "ENG", updatedAt: "-P1D")' --output json

# Include archived issues
mcporter call linear.list_issues team:ENG includeArchived:true --output json
```

## Comments

```bash
# List comments on an issue
mcporter call linear.list_comments issueId:ISSUE_UUID --output json

# Create comment (body is markdown)
mcporter call 'linear.create_comment(issueId: "ISSUE_UUID", body: "Comment text here")' --output json
```

## Teams

```bash
mcporter call linear.list_teams --output json
mcporter call linear.get_team query:ENG --output json
```

## Projects

```bash
# List/get projects
mcporter call linear.list_projects --output json
mcporter call linear.get_project query:infra --output json
mcporter call linear.get_project query:infra includeMilestones:true --output json

# Create project
mcporter call 'linear.create_project(name: "Q2 Platform", description: "Platform improvements for Q2", teams: "[\"ENG\"]")' --output json

# Update project
mcporter call 'linear.update_project(id: "PROJECT_UUID", state: "started", description: "Updated scope")' --output json

# List project labels
mcporter call linear.list_project_labels --output json
```

## Users

```bash
mcporter call linear.list_users --output json
mcporter call linear.get_user query:me --output json
```

## Extended Operations

Milestones, Documents, Attachments, Cycles, Labels, Statuses, and other tools are documented in `references/tool-catalog.md`. Or run `mcporter list linear --all-parameters` to see all tools.

</process>

<tips>
- Use `--output json` for all calls when you need to parse the result.
- Issue identifiers (like ENG-123) work for `get_issue` but UUIDs are needed for `update_issue`, `list_comments`, etc. Get the UUID from `get_issue` first.
- Discover all tools and parameters: `mcporter list linear --all-parameters`
- **Priority values**: 0=None, 1=Urgent, 2=High, 3=Normal, 4=Low
- **State** accepts type names (backlog, unstarted, started, completed, cancelled) or specific state names.
- The `assignee` field accepts "me", a user name, email, or UUID.
- For creating issues with labels, use: `labels:'["Bug","Frontend"]'`
- **Date filters**: Use ISO-8601 durations — `-P1D` (last day), `-P7D` (last week), `-P30D` (last month), `-P90D` (last quarter).
- First call in a session may take ~2s for OAuth token refresh; subsequent calls are faster.
</tips>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/helloworldsungin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
