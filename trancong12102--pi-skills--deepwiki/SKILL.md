---
name: deepwiki
description: > Use when this capability is needed.
metadata:
  author: trancong12102
---

# DeepWiki

## Overview

The DeepWiki skill uses the **mcporter** CLI to query DeepWiki’s public
repository documentation endpoint. Suitable for exploring a GitHub repo:
listing documentation topics, reading documentation content, and asking
questions about the codebase.

Endpoint:

```text
https://mcp.deepwiki.com/mcp
```

## Workflow

### Step 1: Inspect tool signatures

List available tools and their parameters:

```bash
bunx mcporter list https://mcp.deepwiki.com/mcp --all-parameters
```

### Step 2: List documentation topics

```bash
bunx mcporter call https://mcp.deepwiki.com/mcp.read_wiki_structure \
  repoName:owner/repo
```

**Parameters:**

- `repoName` (required): GitHub repository in `owner/repo` format.

### Step 3: Read documentation content

```bash
bunx mcporter call https://mcp.deepwiki.com/mcp.read_wiki_contents \
  repoName:owner/repo
```

**Parameters:**

- `repoName` (required): GitHub repository in `owner/repo` format.

### Step 4: Ask a repo question

```bash
bunx mcporter call https://mcp.deepwiki.com/mcp.ask_question \
  repoName:owner/repo \
  question:"How does the routing layer work?"
```

**Parameters:**

- `repoName` (required): GitHub repository in `owner/repo` format.
- `question` (required): The question to answer about the repo.

## Examples

### List topics for React

```bash
bunx mcporter call https://mcp.deepwiki.com/mcp.read_wiki_structure \
  repoName:facebook/react
```

### Read documentation for React

```bash
bunx mcporter call https://mcp.deepwiki.com/mcp.read_wiki_contents \
  repoName:facebook/react
```

### Ask a question about React

```bash
bunx mcporter call https://mcp.deepwiki.com/mcp.ask_question \
  repoName:facebook/react \
  question:"How is the rendering pipeline structured?"
```

## Tips

- Always start with `mcporter list` to confirm parameter names and defaults.
- Use precise, repo-specific questions for better answers.
- Only public GitHub repositories are supported.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/trancong12102) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
