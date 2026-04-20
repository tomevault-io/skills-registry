---
name: clear-knowledge-base
description: Clears all documentation from the knowledge library and resets the index. Used to prepare for a fresh knowledge base generation. Use when this capability is needed.
metadata:
  author: adamrdrew
---

# Clear Knowledge Base Procedure

This skill clears the knowledge library to prepare for fresh documentation generation.

## Step 1: Ensure directory structure exists

Use the Bash tool to create the knowledge directory if it doesn't exist:

```bash
mkdir -p .claude-agent-sdk-expert/knowledge/library
```

## Step 2: Remove all files from the library

Use the Bash tool to remove all files from the knowledge/library directory except for hidden files:

```bash
find .claude-agent-sdk-expert/knowledge/library -type f ! -name '.*' -delete 2>/dev/null || true
```

## Step 3: Reset the index file

Use the Write tool to reset `.claude-agent-sdk-expert/knowledge/index.md` to its empty state:

```markdown
# Claude Agent SDK Knowledge Index

Consult this index to look up information related to developing for the Claude Agent SDK. All knowledge documents are located in the library directory. When adding new links to this document ensure you put a link with the document path and a short but accurate description of the document's contents.

## Links to Documents

```

## Step 4: Confirm completion

Report back to the user that the knowledge base has been cleared and is ready for regeneration.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adamrdrew) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
