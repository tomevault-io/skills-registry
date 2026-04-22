---
name: db-read
description: Find and read documents from the database matching a query. Searches the index for relevant entries, then reads and synthesizes the content. Use this when the user wants to retrieve stored information. Use when this capability is needed.
metadata:
  author: adamrdrew
---

# Read from Database

Find and retrieve documents matching the user's query.

Use TaskCreate to create a task for each step below, then execute them in order. Mark each task `in_progress` when starting and `completed` when done using TaskUpdate.

## Step 1: Verify Database

Use the Skill tool to invoke the `db-verify` skill.

If verification fails, STOP execution and relay the error message to the user.

## Step 2: Read Index

Use the Read tool to read `database/index.md`.

Parse the index to extract the list of documents. Each document entry follows the format:
```
- [Topic](filename.md) - Description
```

If the index shows "*No documents yet.*" or has no document entries:
- Report to the user: "The database is empty. No documents have been stored yet."
- STOP execution.

## Step 3: Find Relevant Documents

Analyze the user's query and search the index entries for relevant documents.

Match based on:
1. Topic name (exact or partial match)
2. Description keywords
3. Semantic relevance to the query

Create a list of relevant document filenames to read.

If no relevant documents are found:
- Report to the user: "No documents found matching your query. Here's what's in the database:" followed by a list of available topics.
- STOP execution.

## Step 4: Read Documents

For each relevant document identified in the previous step:

Use the Read tool to read `database/<filename>.md`

Collect the content from all relevant documents.

## Step 5: Synthesize and Report

Combine the information from all read documents and present a clear, helpful response to the user's query.

- Answer the user's specific question based on the document content
- Be concise and relevant
- If the documents don't fully answer the query, note what information is available and what's missing

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adamrdrew) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
