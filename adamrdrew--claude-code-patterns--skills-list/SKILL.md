---
name: skills-list
description: Get a list of currently available skills that you can invoke to perform database operations. Always check this list before attempting any operation. Use when this capability is needed.
metadata:
  author: adamrdrew
---

# Available Skills

Any of these skills can be invoked by name via the Skill tool.

## Database Management Skills

```
name: db-verify
description: Verify that the database exists and is properly initialized. Returns status indicating whether the database is ready for use. Use this before any operation that assumes the database exists.
```

```
name: db-init
description: Initialize the database by creating the database/ directory and an empty index.md file. Safe to call if database already exists - will not overwrite existing data.
```

## Data Retrieval Skills

```
name: db-read
description: Find and read documents from the database matching a query. Searches the index for relevant entries, then reads and synthesizes the content. Use this when the user wants to retrieve stored information.
```

```
name: db-list
description: List all documents currently stored in the database. Reads the index and presents a summary of all available documents with their topics and descriptions.
```

## Data Modification Skills

```
name: db-upsert
description: Create a new document or update an existing one. If the topic already exists, updates the document. If it's new, creates the document and adds it to the index. Use this when the user wants to store or update information.
```

```
name: db-delete
description: Remove a document from the database. Deletes the document file and removes its entry from the index. Use this when the user wants to delete stored information.
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adamrdrew) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
