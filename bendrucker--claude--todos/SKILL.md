---
name: gitlabtodos
description: Managing GitLab todos inbox. Use when listing, filtering, or triaging GitLab todos (mark done, mark pending). Use when this capability is needed.
metadata:
  author: bendrucker
---

# GitLab Todos

Manage the GitLab todos inbox via `glab api`.

## Listing

```bash
glab api /todos --paginate | jq '[.[] | select(.state == "pending")]'
```

### Fields

| Field | Description |
|-------|-------------|
| `id` | Todo ID for actions |
| `action_name` | `assigned`, `mentioned`, `build_failed`, `approval_required`, `review_requested`, `directly_addressed` |
| `target_type` | `MergeRequest`, `Issue` |
| `target.title` | Target title |
| `target.web_url` | Browser URL |
| `state` | `pending`, `done` |
| `project.path_with_namespace` | Full project path |

## Actions

```bash
glab api -X POST /todos/{id}/mark_as_done        # Mark single done
glab api -X POST /todos/mark_as_done              # Mark all done
```

## Filtering

Filter in jq after the API call:

```bash
# By action
... | jq '[.[] | select(.state == "pending" and .action_name == "review_requested")]'

# MRs only
... | jq '[.[] | select(.state == "pending" and .target_type == "MergeRequest")]'
```

## Bulk Mark Done

```bash
glab api /todos --paginate | \
  jq -r '[.[] | select(.state == "pending")] | .[].id' | \
  xargs -I {} glab api -X POST /todos/{}/mark_as_done
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bendrucker) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
