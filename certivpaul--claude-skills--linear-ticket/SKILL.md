---
name: linear-ticket
description: This skill should be used when the user wants to file a Linear ticket, create an issue, or report a bug. It takes minimal input from the user, intelligently researches the codebase for relevant context, and files a well-composed ticket to the appropriate Linear project. Triggers on phrases like "file a ticket", "create a Linear issue", "report this bug", "open an issue for", or "log this in Linear". Use when this capability is needed.
metadata:
  author: certivpaul
---

# Linear Ticket

File Linear tickets with minimal user input. The skill automatically detects the appropriate project from the git repo and gathers relevant context from the codebase.

## Workflow

### Step 1: Gather User Input

Prompt the user for the ticket details if not already provided:
- **Required**: Brief description of the issue or feature request
- **Optional**: Priority, labels, assignee (ask only if user seems to want to specify)

Keep the prompt minimal - the goal is to require as little input as possible.

### Step 2: Determine the Linear Project

Use a cascading detection strategy to find the appropriate project:

#### 2a. Check if current directory is a git repo

```bash
git rev-parse --is-inside-work-tree 2>/dev/null && git remote get-url origin 2>/dev/null | sed 's/.*[\/:]//;s/\.git$//'
```

If this succeeds, extract the repo name and proceed to matching.

#### 2b. If not in a git repo, search for repos in the current directory

Look for git repositories in subdirectories:

```bash
find . -maxdepth 3 -type d -name ".git" 2>/dev/null | head -10
```

For each found repo, extract its name:

```bash
git -C <repo-path> remote get-url origin 2>/dev/null | sed 's/.*[\/:]//;s/\.git$//'
```

If multiple repos are found, present them to the user to choose which one the ticket relates to.

#### 2c. Match repo name to Linear project

Once a repo name is identified:
1. Use `mcp__plugin_linear_linear__list_projects` to fetch available projects
2. Look for exact match (case-insensitive) between repo name and project name
3. If no exact match, search for partial matches (repo name contained in project name or vice versa)
4. If a single match is found, use it
5. If multiple matches, present options to the user

#### 2d. Fallback: Ask user to select a project

If no repo is found or no matching project exists:
1. Use `mcp__plugin_linear_linear__list_projects` to list available projects
2. Ask the user which project to file the ticket against using AskUserQuestion

### Step 3: Assess Context Relevance

Evaluate whether the current conversation context is relevant to the ticket being filed:

1. Review the recent conversation history
2. Determine if it relates to the ticket description (e.g., error messages, code discussions, debugging sessions)
3. If relevant, extract useful information:
   - Error messages and stack traces
   - File paths and line numbers discussed
   - Code snippets that were examined
   - Steps that were attempted

**Important**: The current context may have nothing to do with the ticket. Do not force irrelevant context into the ticket. If the user is filing a ticket about something unrelated to the current conversation, focus solely on researching the codebase based on their description.

### Step 4: Research the Codebase

Based on the ticket description, investigate the codebase to gather relevant context:

1. Search for related files, functions, or components mentioned in the description
2. Find existing code patterns or implementations related to the issue
3. Identify relevant file paths that should be referenced in the ticket
4. Look for related tests, documentation, or configuration

Use Grep and Glob to search efficiently. Do not over-research - gather just enough context to write a clear, actionable ticket.

### Step 5: Compose the Ticket

Write a well-structured ticket with:

**Title**: Clear, concise summary (imperative mood, e.g., "Fix authentication timeout on slow connections")

**Description** (Markdown format):
```markdown
## Summary
[1-2 sentence description of the issue or request]

## Context
[Relevant background information, code locations, or related components]

## Details
[Specific technical details, error messages, or reproduction steps if applicable]

## Relevant Files
- `path/to/file.ts` - [brief reason why relevant]
```

Adapt the structure based on ticket type:
- **Bug**: Include reproduction steps, expected vs actual behavior
- **Feature**: Include motivation, proposed approach if known
- **Task**: Include clear acceptance criteria

### Step 6: File the Ticket

Use `mcp__plugin_linear_linear__create_issue` to create the ticket:

```
title: [composed title]
description: [composed description]
team: [team from the project]
project: [identified project name or ID]
```

After filing, display:
- The ticket identifier (e.g., ENG-123)
- A link to the ticket
- A brief summary of what was filed

## Tips

- Keep tickets focused - one issue per ticket
- Use code blocks for file paths, error messages, and code snippets
- Reference specific line numbers when relevant
- Include links to related tickets if the user mentions them
- Default to normal priority unless the user indicates urgency

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/certivpaul) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
