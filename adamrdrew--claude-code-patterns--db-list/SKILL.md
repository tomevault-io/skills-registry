---
name: db-list
description: List all documents currently stored in the database. Reads the index and presents a summary of all available documents with their topics and descriptions. Use when this capability is needed.
metadata:
  author: adamrdrew
---

# List Database Documents

Display all documents stored in the database.

Use TaskCreate to create a task for each step below, then execute them in order. Mark each task `in_progress` when starting and `completed` when done using TaskUpdate.

## Step 1: Verify Database

Use the Skill tool to invoke the `db-verify` skill.

If verification fails, STOP execution and relay the error message to the user.

## Step 2: Read Index

Use the Read tool to read `database/index.md`.

## Step 3: Parse Document List

Extract all document entries from the index. Each entry follows the format:
```
- [Topic](filename.md) - Description
```

If the index shows "*No documents yet.*" or has no document entries:
- Report to the user: "The database is empty. No documents have been stored yet."
- STOP execution.

## Step 4: Present Document List

Format and present the list of documents to the user:

```
## Stored Documents

| Topic | Description |
|-------|-------------|
| Pets | Information about user's pets |
| Recipes | Favorite recipes and cooking notes |
...
```

Include a count of total documents at the end.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adamrdrew) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
