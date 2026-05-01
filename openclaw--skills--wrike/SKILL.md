---
name: wrike
description: Manage projects, tasks, and workflows via Wrike API. Create tasks, update statuses, and track work. Use when this capability is needed.
metadata:
  author: openclaw
---
# Wrike
Project management platform.
## Environment
```bash
export WRIKE_ACCESS_TOKEN="xxxxxxxxxx"
```
## List Folders
```bash
curl "https://www.wrike.com/api/v4/folders" -H "Authorization: Bearer $WRIKE_ACCESS_TOKEN"
```
## Create Task
```bash
curl -X POST "https://www.wrike.com/api/v4/folders/{folderId}/tasks" \
  -H "Authorization: Bearer $WRIKE_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"title": "New Task", "status": "Active"}'
```
## List Tasks
```bash
curl "https://www.wrike.com/api/v4/tasks" -H "Authorization: Bearer $WRIKE_ACCESS_TOKEN"
```
## Links
- Docs: https://developers.wrike.com

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
