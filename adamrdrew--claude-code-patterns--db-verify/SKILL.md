---
name: db-verify
description: Verify that the database exists and is properly initialized. Returns status indicating whether the database is ready for use. Use this before any operation that assumes the database exists. Use when this capability is needed.
metadata:
  author: adamrdrew
---

# Verify Database

Check that the database directory and index file exist and are properly structured.

Use TaskCreate to create a task for each step below, then execute them in order. Mark each task `in_progress` when starting and `completed` when done using TaskUpdate.

## Step 1: Check Database Directory

Use the Bash tool to check if the `database/` directory exists:

```bash
[ -d "database" ] && echo "EXISTS" || echo "MISSING"
```

If the output is "MISSING":
- Report to the caller: "Database not initialized. Use the `db-init` skill to create it."
- STOP execution. Do not proceed to further steps.

## Step 2: Check Index File

Use the Read tool to read `database/index.md`.

If the file does not exist or returns an error:
- Report to the caller: "Database index is missing. Use the `db-init` skill to reinitialize."
- STOP execution. Do not proceed to further steps.

## Step 3: Validate Index Structure

Verify the index file contains the expected structure:
- Should have a `# Database Index` heading
- Should have a `## Documents` section

If the structure is malformed:
- Report to the caller: "Database index is malformed. Consider reinitializing with `db-init`."
- STOP execution.

## Step 4: Report Success

If all checks pass, report to the caller: "Database verified and ready."

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adamrdrew) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
