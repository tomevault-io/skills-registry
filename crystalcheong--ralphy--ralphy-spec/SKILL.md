---
name: ralphy-spec
description: Describe what this skill does and when to use it. Include keywords that help agents identify relevant tasks. Use when this capability is needed.
metadata:
  author: crystalcheong
---

Plan and draft a PRD and task breakdowns for ONE entry from the backlog.

## Variables
BACKLOG_LIST = RALPHY_DIR/backlog.json
PRD_DOC = RALPHY_DIR/plans/<id>.prd.md (where <id> is the "id" field in the backlog entry)
TASK_LIST = RALPHY_DIR/plans/<id>.tasks.json (where <id> is the "id" field in the backlog entry)
BACKLOG_BRANCH = backlog/<id> (where <id> is the "id" field in the backlog entry)

## Steps
1. Study BACKLOG_LIST and pick then most important thing to do
2. Create the PRD_DOC. Interview the user for any unclear details before drafting.
3. Break down the PRD into a set of tasks with success criteria and save to TASK_LIST. 
3.1. Assign parallel_group numbers to each task to enable parallel execution whenever possible, else set to 0.
5. Ask for user confirmation to git branch out BACKLOG_BRANCH. Once confirmed, create the branch and commit PRD_DOC and TASK_LIST to the repo.
6. Return only the command below to run ralphy with the generated PRD_DOC and TASK_LIST. Do not include any other text.
```bash
ralphy --opencode --json <TASK_LIST path> --base-branch <BACKLOG_BRANCH> --branch-per-task --parallel --max-parallel 5 --sandbox -v
```

```json
{
  "tasks": [
    {
      "title": "create auth",
      "completed": false,
      "parallel_group": 1,
      "description": "Optional details"
    }
  ]
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/crystalcheong) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
