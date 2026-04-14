---
name: super-save
description: Save important project knowledge to memory. Use when user wants to preserve architectural decisions, significant bug fixes, design patterns, or important implementation details for team reference. Use when this capability is needed.
metadata:
  author: supermemoryai
---

# Super Save

Save important project knowledge based on what the user wants to preserve.

## Step 1: Understand User Request

Analyze what the user is asking to save from the conversation.

## Step 2: Format Content

```
[SAVE:<username>:<date>]

<Username> wanted to <goal/problem>.

Claude suggested <approach/solution>.

<Username> decided to <decision made>.

<key details, files if relevant>

[/SAVE]
```

Example:
```
[SAVE:prasanna:2026-02-04]

Prasanna wanted to create a skill for saving project knowledge.

Claude suggested using a separate container tag (repo_<hash>) for shared team knowledge.

Prasanna decided to keep it simple - no transcript fetching, just save what user asks for.

Files: src/save-project-memory.js, src/lib/container-tag.js

[/SAVE]
```

Keep it natural. Capture the conversation flow.

## Step 3: Save

```bash
node "${CLAUDE_PLUGIN_ROOT}/scripts/save-project-memory.cjs" "FORMATTED_CONTENT"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/supermemoryai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
