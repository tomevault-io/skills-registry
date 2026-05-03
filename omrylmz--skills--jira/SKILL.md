---
name: jira
description: This skill should be used when the user asks to "create a Jira ticket", "list Jira issues", "update issue status", "move ticket to Done", "add comment to issue", "find bugs in Jira", "check my Jira tasks", "transition issue", "assign issue", "update issue priority", "search users", "delete issue", or mentions JQL queries. Provides scripts for CRUD operations on Jira issues via REST API. Use when this capability is needed.
metadata:
  author: omrylmz
---

# Jira Integration Skill

Full CRUD operations on Jira issues via shell scripts wrapping the Atlassian REST API.

## Scripts Reference

| Script | Purpose | Usage |
|--------|---------|-------|
| `create-issue.sh` | Create issue | `PROJECT "Summary" ["Desc"] [Type]` |
| `list-issues.sh` | Query with JQL | `"JQL_QUERY" [MAX]` |
| `get-issue.sh` | View details | `ISSUE_KEY` |
| `update-issue.sh` | Modify fields | `ISSUE_KEY --field value...` |
| `transition-issue.sh` | Change status | `ISSUE_KEY "STATUS"` or `"?"` |
| `assign-issue.sh` | Assign user | `ISSUE_KEY ACCOUNT_ID\|--me\|--unassign` |
| `add-comment.sh` | Add comment | `ISSUE_KEY "Text"` |
| `search-users.sh` | Find users | `"query" [MAX]` |
| `get-projects.sh` | List projects | `[--keys-only]` |
| `delete-issue.sh` | Delete issue | `ISSUE_KEY [--force]` |

## Quick Start

### Create and Manage Issues

```bash
# Create a bug
./scripts/create-issue.sh MRNK "Login button unresponsive" "First tap ignored" Bug

# Create a task
./scripts/create-issue.sh MRNK "Add dark mode" "" Task

# Update fields
./scripts/update-issue.sh MRNK-104 --priority High --labels "ui,mobile"

# Assign to yourself
./scripts/assign-issue.sh MRNK-104 --me
```

### Workflow Operations

```bash
# Start work
./scripts/transition-issue.sh MRNK-104 "In Progress"

# Check available transitions
./scripts/transition-issue.sh MRNK-104 "?"

# Complete and document
./scripts/transition-issue.sh MRNK-104 "Done"
./scripts/add-comment.sh MRNK-104 "Fixed in commit abc123"
```

### Query Issues

```bash
# Open bugs
./scripts/list-issues.sh "project=MRNK AND type=Bug AND status!='Done'"

# My issues
./scripts/list-issues.sh "project=MRNK AND assignee=currentUser()"

# Recent updates
./scripts/list-issues.sh "project=MRNK AND updated >= -7d ORDER BY updated DESC"

# High priority
./scripts/list-issues.sh "project=MRNK AND priority IN (High, Highest) AND status!='Done'"
```

### Update Issue Fields

```bash
# Change summary
./scripts/update-issue.sh MRNK-104 --summary "New title"

# Change description
./scripts/update-issue.sh MRNK-104 --description "Updated description"

# Set priority
./scripts/update-issue.sh MRNK-104 --priority High

# Replace labels
./scripts/update-issue.sh MRNK-104 --labels "bug,urgent"

# Add labels (keep existing)
./scripts/update-issue.sh MRNK-104 --add-labels "reviewed"

# Multiple fields at once
./scripts/update-issue.sh MRNK-104 --priority High --labels "bug,ui" --summary "Updated"
```

### User Assignment

```bash
# Find user account ID
./scripts/search-users.sh "john"

# Assign by account ID
./scripts/assign-issue.sh MRNK-104 "5b10a2844c20165700ede21g"

# Assign to yourself
./scripts/assign-issue.sh MRNK-104 --me

# Unassign
./scripts/assign-issue.sh MRNK-104 --unassign
```

## Setup

### Environment Variables

```bash
export ATLASSIAN_SITE_URL="https://yoursite.atlassian.net"
export ATLASSIAN_USER_EMAIL="your@email.com"
export ATLASSIAN_API_TOKEN="your-api-token"
```

Get token: https://id.atlassian.com/manage-profile/security/api-tokens

### Dependency

```bash
brew install jq  # macOS
```

## Troubleshooting

| Error | Solution |
|-------|----------|
| Missing env variables | Set ATLASSIAN_SITE_URL, ATLASSIAN_USER_EMAIL, ATLASSIAN_API_TOKEN |
| HTTP 401 | Check API token validity |
| HTTP 404 | Verify issue key exists |
| Invalid transition | Run with `"?"` to see available statuses |

## Additional Resources

### Reference Files
- **`references/jql-patterns.md`** - Comprehensive JQL syntax and query examples
- **`references/workflows.md`** - Common agent workflow patterns (bug fix, feature, triage)

### Example Scripts
- **`examples/bug-fix-workflow.sh`** - End-to-end bug fix process
- **`examples/create-bug-report.sh`** - Creating detailed bug reports

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/omrylmz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
