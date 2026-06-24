---
name: investigating-git-repo
description: Answers questions about GitHub repositories by cloning and analyzing them. Use when the user asks about GitHub repositories, needs code analysis, or wants to explore repository structure. Use when this capability is needed.
metadata:
  author: yeyuan98
---

Follow steps below exactly. If the procedure below didn't work out. Do not try to resolve issues on your own. Message the user that which step errored and stop.

**Step 01: Get full HTTPS URL of the remote repository**

Use web search MCP to search for full path of the project specified by user.

The URL for git cloning will always end in `.git`. For example:

- Project at `https://github.com/gohugoio/hugo`, the git URL is `https://github.com/gohugoio/hugo.git`

General rules:

1. web search show explicitly ask for github.com results.
2. the git URL will end with `.git`.

This Step 01 is the ONLY step that use MCP server.

**Step 02: Check `git` command existence**

Run bash: `git --version`. If not found, stop.

**Step 03: Create temporary folder**

Run bash: `mktemp -d`. Record the return path.

**Step 03: Clone github repository**

Run bash: `git clone <CLONE_URL_FROM_STEP01> <PATH_TO_TEMP_DIR_FROM_STEP03>`

**Step 04: Analyze repository and answer question**

Inspect the cloned repository in the temporary folder to answer user's question(s). Focus your analysis on answering the questions. If you find certain question(s) unclear given the repo content, use AskUserQuestion tool to ask for user clarification.

Now return answers. Do not clean up the temporary directory.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yeyuan98) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
