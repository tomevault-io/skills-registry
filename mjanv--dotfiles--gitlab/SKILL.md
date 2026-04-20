---
name: gitlab
description: Interact with GitLab for repository management, merge requests, pipelines, and CI/CD. Use when the user asks about GitLab projects, MRs, pipelines, or code search. Use when this capability is needed.
metadata:
  author: mjanv
---

# GitLab Skill

You are a specialized assistant for managing repositories and CI/CD in GitLab. This skill enables querying projects, merge requests, pipelines, and searching code.

## When to Use This Skill

Activate this skill when the user:
- Asks about GitLab projects or repositories
- Wants to see merge requests or pipelines
- Needs to search code or files
- Asks about CI/CD status or job logs
- Wants to browse repository contents
- Mentions "GitLab", "MR", "pipeline", or "CI/CD"

## Prerequisites

Before using this skill, ensure:
1. The `~/.claude/.env` file exists with `GITLAB_API_TOKEN`
2. Python 3.x is installed
3. Network access to GitLab instance (https://gitlab.lan.athonet.com)

## Skill Structure

```
.claude/skills/gitlab/
├── SKILL.md                    # This file
├── requirements.txt            # Python dependencies
└── scripts/
    ├── gitlab_client.py        # Core GitLab REST API client
    └── gitlab_helper.py        # High-level repository operations
```

Project root contains:
- `~/.claude/.env` - API credentials (GITLAB_API_TOKEN)

## Quick Start

### Using GitLabHelper (Recommended)

```python
import sys
sys.path.insert(0, '.claude/skills/gitlab/scripts')

from gitlab_helper import GitLabHelper

helper = GitLabHelper()
helper.connect()

# List projects
projects = helper.list_projects(membership=True)

# Get a specific project
project = helper.get_project("group/project-name")

# Get file content
content = helper.get_file_content("group/project", "README.md", ref="main")

# List files in repository
files = helper.list_files("group/project", path="src/", recursive=True)

# Get recent commits
commits = helper.get_commits("group/project", branch="main", limit=10)

# List open merge requests
mrs = helper.list_merge_requests(project_path="group/project", state="opened")

# Get my MRs
my_mrs = helper.get_my_merge_requests()

# List pipelines
pipelines = helper.list_pipelines("group/project", status="success")

# Get pipeline jobs
jobs = helper.get_pipeline_jobs("group/project", pipeline_id=12345)

# Get job log
log = helper.get_job_log("group/project", job_id=67890)

# Search code
results = helper.search_code("function_name", project_path="group/project")
```

### Using GitLabClient Directly

```python
import sys
sys.path.insert(0, '.claude/skills/gitlab/scripts')

from gitlab_client import GitLabClient

client = GitLabClient()
client.authenticate()

# Get current user
user = client.get_current_user()

# Get projects
projects = client.get_projects(membership=True, per_page=20)

# Get project by path
project = client.get_project_by_path("group/project")

# Get branches
branches = client.get_branches("group/project")

# Get tags
tags = client.get_tags("group/project")

# Get file content
content = client.get_file_content("group/project", "src/main.py", ref="main")

# Get repository tree
tree = client.get_tree("group/project", path="src/", recursive=True)

# Get merge requests
mrs = client.get_merge_requests(project_id="group/project", state="opened")

# Get pipelines
pipelines = client.get_pipelines("group/project", status="running")

# Global search
results = client.search("query", scope="blobs", project_id="group/project")
```

## Available Operations

### GitLabHelper Methods

| Method | Description |
|--------|-------------|
| `connect()` | Initialize GitLab API client with credentials |
| `list_projects(search, owned, membership, starred)` | List accessible projects |
| `get_project(project_path)` | Get project by path |
| `search_projects(query)` | Search for projects |
| `get_file_content(project, path, ref)` | Get file content |
| `list_files(project, path, ref, recursive)` | List repository files |
| `get_branches(project)` | Get project branches |
| `get_tags(project)` | Get project tags |
| `get_commits(project, branch)` | Get recent commits |
| `list_merge_requests(project, state)` | List merge requests |
| `get_merge_request(project, iid)` | Get single MR |
| `get_my_merge_requests()` | Get MRs authored by me |
| `get_assigned_merge_requests()` | Get MRs assigned to me |
| `list_pipelines(project, status, ref)` | List pipelines |
| `get_pipeline(project, id)` | Get single pipeline |
| `get_pipeline_jobs(project, pipeline_id)` | Get pipeline jobs |
| `get_job_log(project, job_id)` | Get job log |
| `get_latest_pipeline(project, ref)` | Get latest pipeline |
| `list_issues(project, state, labels)` | List issues |
| `search_code(query, project)` | Search code |

### GitLabClient Methods

| Method | Description |
|--------|-------------|
| `authenticate()` | Load credentials and verify API access |
| `get_current_user()` | Get authenticated user |
| `get_projects(search, owned, membership, starred)` | Get projects |
| `get_project(project_id)` | Get project by ID |
| `get_project_by_path(path)` | Get project by path |
| `get_branches(project_id, search)` | Get branches |
| `get_branch(project_id, branch)` | Get single branch |
| `get_tags(project_id, search)` | Get tags |
| `get_commits(project_id, ref_name, since, until)` | Get commits |
| `get_commit(project_id, sha)` | Get single commit |
| `get_file(project_id, file_path, ref)` | Get file info |
| `get_file_content(project_id, file_path, ref)` | Get raw file content |
| `get_tree(project_id, path, ref, recursive)` | Get repository tree |
| `get_merge_requests(project_id, state, scope)` | Get merge requests |
| `get_merge_request(project_id, mr_iid)` | Get single MR |
| `get_merge_request_changes(project_id, mr_iid)` | Get MR diff |
| `get_issues(project_id, state, labels)` | Get issues |
| `get_issue(project_id, issue_iid)` | Get single issue |
| `get_pipelines(project_id, status, ref)` | Get pipelines |
| `get_pipeline(project_id, pipeline_id)` | Get single pipeline |
| `get_pipeline_jobs(project_id, pipeline_id)` | Get pipeline jobs |
| `get_job_log(project_id, job_id)` | Get job log |
| `get_groups(search, owned)` | Get groups |
| `get_group(group_id)` | Get single group |
| `get_group_projects(group_id, include_subgroups)` | Get group projects |
| `search(query, scope, project_id, group_id)` | Global search |

## Common Tasks

### 1. Check Pipeline Status

```python
# Get latest pipeline for a branch
pipeline = helper.get_latest_pipeline("group/project", ref="main")
print(f"Pipeline #{pipeline.id}: {pipeline.status}")

# Get failed jobs
if pipeline.status == "failed":
    jobs = helper.get_pipeline_jobs("group/project", pipeline.id)
    for job in jobs:
        if job["status"] == "failed":
            print(f"Failed: {job['name']} in stage {job['stage']}")
            log = helper.get_job_log("group/project", job["id"])
            print(log[-1000:])  # Last 1000 chars
```

### 2. Review Merge Requests

```python
# Get open MRs assigned to me
mrs = helper.get_assigned_merge_requests()
for mr in mrs:
    print(f"!{mr.iid}: {mr.title}")
    print(f"  {mr.source_branch} -> {mr.target_branch}")
    print(f"  Author: {mr.author}")
    print(f"  URL: {mr.web_url}")
```

### 3. Browse Repository

```python
# List files in a directory
files = helper.list_files("group/project", path="src/", ref="main")
for f in files:
    print(f"{'[D]' if f['type'] == 'tree' else '[F]'} {f['name']}")

# Read a file
content = helper.get_file_content("group/project", "src/config.py", ref="main")
print(content)
```

### 4. Search Code

```python
# Search for a function across projects
results = helper.search_code("authenticate_user")
for r in results:
    print(f"{r['path']} in project {r['project_id']}")
    print(f"  {r['data'][:100]}...")
```

### 5. Check Recent Activity

```python
# Get recent commits
commits = helper.get_commits("group/project", branch="main", limit=5)
for c in commits:
    print(f"{c['id']} - {c['title']} ({c['author']})")

# Get recent tags
tags = helper.get_tags("group/project", limit=5)
for t in tags:
    print(f"{t['name']} - {t['message']}")
```

## Error Handling

Common errors and solutions:

| Error | Cause | Solution |
|-------|-------|----------|
| 401 Unauthorized | Invalid token | Check GITLAB_TOKEN in .env |
| 403 Forbidden | No project access | Request project permissions |
| 404 Not Found | Project/MR doesn't exist | Verify project path |
| SSL Error | Self-signed certificate | Set verify_ssl=False (default) |

## Running Scripts

```bash
# Run the helper example
python .claude/skills/gitlab/scripts/gitlab_helper.py

# Run client directly with search
python .claude/skills/gitlab/scripts/gitlab_client.py --search "project-name"
```

## Configuration

The client reads settings from environment:

- `GITLAB_API_TOKEN` - Personal Access Token **required**
- `GITLAB_URL` - GitLab instance URL (default: https://gitlab.lan.athonet.com)

### Creating a Personal Access Token

1. Go to GitLab → User Settings → Access Tokens
2. Create token with scopes: `api`, `read_api`, `read_repository`
3. Add to `~/.claude/.env`: `GITLAB_API_TOKEN=glpat-xxxxxxxxxxxx`

## API Limits

- Maximum 100 results per page
- Rate limits apply per GitLab instance configuration
- Large file downloads may timeout

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mjanv) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
