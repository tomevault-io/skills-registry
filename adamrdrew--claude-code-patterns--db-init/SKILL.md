---
name: db-init
description: Initialize the database by creating the database/ directory and an empty index.md file. Safe to call if database already exists - will not overwrite existing data. Use when this capability is needed.
metadata:
  author: adamrdrew
---

# Initialize Database

Create the database directory structure and initialize an empty index.

Use TaskCreate to create a task for each step below, then execute them in order. Mark each task `in_progress` when starting and `completed` when done using TaskUpdate.

## Step 1: Check If Already Initialized

Use the Bash tool to check if the database directory exists:

```bash
[ -d "database" ] && echo "EXISTS" || echo "MISSING"
```

If the output is "EXISTS":
- Use the Read tool to check if `database/index.md` exists
- If index exists, report: "Database already initialized. No action needed."
- STOP execution. Do not proceed to further steps.

## Step 2: Create Database Directory

Use the Bash tool to create the database directory:

```bash
mkdir -p database
```

## Step 3: Create Index File

Use the Write tool to create `database/index.md` with the following content:

```markdown
# Database Index

This file tracks all documents stored in the database.

## Documents

<!-- Documents will be listed here as: - [Topic](filename.md) - Description -->

*No documents yet.*
```

## Step 4: Verify Initialization

Use the Read tool to read `database/index.md` and verify it was created correctly.

## Step 5: Report Success

Report to the caller: "Database initialized successfully. The database is ready to store documents."

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adamrdrew) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
