---
name: ralphy-task
description: Describe what this skill does and when to use it. Include keywords that help agents identify relevant tasks. Use when this capability is needed.
metadata:
  author: crystalcheong
---

Grab a task fron the TASK_LIST and follow the instructions below to complete it.

## Variables
BACKLOG_LIST = RALPHY_DIR/backlog.json
BACKLOG_BRANCH = backlog/<id> (where <id> is the "id" field in the backlog entry)
PRD_DOC = RALPHY_DIR/plans/<id>.prd.md (where <id> is the "id" field in the backlog entry)
TASK_LIST = RALPHY_DIR/plans/<id>.tasks.json (where <id> is the "id" field in the backlog entry)
TASK_BRANCH = task/<task_title> (where <task_title> is the "title" field in the task entry)
PROGRESS_FILE = RALPHY_DIR/progress.txt

## Definitions
- ABORT IMMEDIATELY: Stop all current operations and exit the task branch without making any changes. Kill the agent here.
- COMPLETED TASK: A task is considered completed when the <task_title> is updated in PROGRESS_FILE, the task status is updated to completed in TASK_LIST.
- PICKED TASK: A task is considered picked when you have switched to its TASK_BRANCH. This serves as a HARD LOCK for the specific task to prevent conflicts with other agents. You SHOULD only pick a task that is not a COMPLETED TASK. Once picked, you MUST work on the task and follow the SOP below to complete it. You MUST NOT pick another task without completing the PICKED TASK. You MUST NOT abandon a PICKED TASK until it is a COMPLETED TASK.

## STANDARD OPERATING PROCEDURE (SOP) FOR WORKING ON A TASK 
IMPORTANT: DO NOT DEVIATE FROM THIS SOP. FOLLOW EACH STEP IN ORDER. DO NOT SKIP ANY STEP. FAILURE TO FOLLOW THIS SOP WILL RESULT IN YOUR DEATH. WHEN INDICATED WITH "ANS", OUTPUT YOUR ANSWER IN THE FORMAT OF "Q1: <your answer to question 1>, Q2: <your answer to question 2>, ...". WHEN INDICATED WITH "OUTPUT", OUTPUT THE REQUESTED INFORMATION IN THE FORMAT SPECIFIED.

1. Git switch to BACKLOG_BRANCH. If BACKLOG_BRANCH does not exist, ABORT IMMEDIATELY.
2. PICK ONE TASK from TASK_LIST that is not marked as completed to work on.  
   (ANS: Output the title and id of the task you picked).  
   If all tasks are completed, EXIT the agent here.
3. Check if the TASK_BRANCH for the task you picked already exists.  
   If it does, ABORT IMMEDIATELY to avoid conflicts and pick another task, repeating check 2.  
   (ANS: Output yes or no for whether the TASK_BRANCH already exists, and whether you are aborting to pick another task)
4. If the TASK_BRANCH does not exist, create a new TASK_BRANCH from BACKLOG_BRANCH and switch to it.  
   This serves as a HARD LOCK for the specific task to prevent conflicts with other agents.
5. Now you should be on the TASK_BRANCH for the PICKED TASK.  
   You MUST work on the task according to its description and success criteria.  
   You MUST NOT pick another task without completing the PICKED TASK.  
   You MUST NOT abandon a PICKED TASK until it is a COMPLETED TASK.
   (ANS: Acknowledge that you understand the task description and success criteria, and outline your plan to work on the task step by step)


### Testing, Linting, and Committing the COMPLETED TASK
IMPORTANT: DO NOT SPIN UP DOCKER CONTAINERS, DEVCONTAINERS FOR TESTING AND LINTING

#### RULES:
1. Lint ONLY the files you created/updated with the following command:
```bash
devcontainer exec --workspace-folder . bash -lc "uv run black src/ tests/ && uv run ruff check --fix src/ tests/"'
```
2. Test ONLY the test files you created/updates with the following command:
```bash
devcontainer exec --workspace-folder . uv run pytest
```
3. Update PROGRESS_FILE with the task title
4. Update TASK_LIST with the task status as completed, and include a concise summary of the implementation and any important details for future contributors to know.
5. Git commit your changes in TASK_BRANCH with a commit message that includes the task title and id, and merge the TASK_BRANCH back to BACKLOG_BRANCH.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/crystalcheong) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
