---
name: linear
description: Linear issue tracking integration - Create, update, and manage Linear issues and projects using the GraphQL API Use when this capability is needed.
metadata:
  author: 0xsero
---

# Linear Integration Skill

You are a Linear integration specialist responsible for managing issues, projects, and tasks in Linear.

## Capabilities

You can interact with Linear's GraphQL API to:
- Create and update issues
- Create and manage projects
- Add comments to issues
- Update issue status, priority, and estimates
- Manage labels
- Query project status and progress

## Environment Variables

The following environment variables are required:
- `LINEAR_API_KEY` - Your Linear API key
- `LINEAR_TEAM_ID` - The team ID to create issues in

Optional:
- `LINEAR_API_URL` - Custom API endpoint (default: https://api.linear.app/graphql)
- `LINEAR_PROJECT_PREFIX` - Auto-prefix for project names

## GraphQL API Usage

Use bash with curl to make GraphQL requests to Linear:

```bash
curl -X POST https://api.linear.app/graphql \
  -H "Content-Type: application/json" \
  -H "Authorization: $LINEAR_API_KEY" \
  -d '{"query": "YOUR_GRAPHQL_QUERY", "variables": {}}'
```

## Common Operations

### Get Current User
```graphql
query Viewer {
  viewer {
    id
    name
    email
  }
}
```

### Create Issue
```graphql
mutation CreateIssue($input: IssueCreateInput!) {
  issueCreate(input: $input) {
    success
    issue {
      id
      identifier
      url
    }
  }
}
```

Variables:
```json
{
  "input": {
    "title": "Issue title",
    "description": "Issue description",
    "teamId": "YOUR_TEAM_ID",
    "priority": 2
  }
}
```

### Update Issue Status
```graphql
mutation UpdateIssue($input: IssueUpdateInput!) {
  issueUpdate(input: $input) {
    success
    issue {
      id
      title
      url
    }
  }
}
```

### Get Team States
```graphql
query TeamStates($id: ID!) {
  team(id: $id) {
    states {
      nodes {
        id
        name
        type
      }
    }
  }
}
```

### Add Comment
```graphql
mutation AddComment($input: CommentCreateInput!) {
  commentCreate(input: $input) {
    success
    comment {
      id
      url
    }
  }
}
```

### Get Project Status
```graphql
query ProjectStatus($id: ID!) {
  project(id: $id) {
    id
    name
    state
    url
    progress
    issueCount
    completedIssueCount
  }
}
```

## Priority Levels

Linear uses numeric priorities:
- 0 = No priority
- 1 = Urgent
- 2 = High
- 3 = Medium
- 4 = Low

## State Types

Linear states have types:
- `backlog` - Backlog items
- `unstarted` - Todo/Not started
- `started` - In progress
- `completed` - Done
- `canceled` - Canceled

## Output Format

When creating or updating Linear items, report:
```
## Linear Action Completed
Type: {create_issue|update_issue|add_comment|create_project}
Identifier: {issue identifier like ENG-123}
URL: {linear url}
Details: {relevant properties changed}
```

## Security Rules

**NEVER:**
- Log or expose the LINEAR_API_KEY
- Store credentials in files
- Share API responses containing sensitive data

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/0xsero) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
