---
name: linear-research-doc
description: Create a thoroughly researched Linear document. Use when the user wants to document work they've completed, create a technical spec, or compile research into a Linear doc. Triggers on "create a linear doc", "document this in linear", "write up what we did", "create research doc", or "linear document". Use when this capability is needed.
metadata:
  author: certivpaul
---

# Linear Research Document Generator

Create a comprehensive, well-researched Linear document by gathering context from multiple sources.

## Overview

This skill creates a Linear document that synthesizes:
- **Recent work context** - what the user was just working on
- **Codebase research** - relevant code, patterns, and architecture
- **Web research** - best practices, documentation, related articles
- **PR information** - any related pull requests
- **Linear tickets** - any related existing issues

## Workflow

### Phase 1: Gather Context

#### 1.1 Determine the Topic

If the user provided a topic via `$ARGUMENTS`, use that. Otherwise, analyze recent work:

```bash
# Check recent git activity
git log --oneline -20 2>/dev/null || echo "No git history"
git diff --stat HEAD~5 2>/dev/null || echo "No recent changes"
git branch --show-current 2>/dev/null || echo "Not in a git repo"
```

Ask the user to confirm or clarify the topic if it's ambiguous.

#### 1.2 Gather Git/PR Context

Search for related PRs and recent commits:

```bash
# Get current branch and recent commits
git log --oneline -10 --format="%h %s" 2>/dev/null

# Check for open PRs on current branch
gh pr list --head $(git branch --show-current 2>/dev/null) --json number,title,url,body 2>/dev/null

# Get PR details if one exists
gh pr view --json number,title,body,url,additions,deletions,changedFiles,commits 2>/dev/null
```

#### 1.3 Search Linear for Related Tickets

Use the Linear MCP tools to find related issues:

1. Search for issues matching the topic keywords
2. Look for issues assigned to the current user
3. Check for issues updated recently that might be related

### Phase 2: Research

#### 2.1 Codebase Research

Use exploration tools to understand the relevant code:

1. **Find related files** - Use Glob and Grep to locate relevant code
2. **Read key files** - Understand the implementation details
3. **Identify patterns** - Note architectural decisions and conventions
4. **Find dependencies** - Understand what the code interacts with

#### 2.2 Web Research

Use WebSearch and WebFetch to gather external information:

1. **Official documentation** - For any libraries/frameworks involved
2. **Best practices** - Industry standards and recommendations
3. **Similar implementations** - How others solved similar problems
4. **Security considerations** - Any relevant security guidance

### Phase 3: Compile the Document

Create a Linear document with the following structure:

```markdown
# [Topic Title]

## Summary
[2-3 sentence overview of what this document covers]

## Background
[Context about why this work was done, what problem it solves]

## Technical Details

### Implementation
[Key technical decisions, architecture, code patterns]

### Key Files
[List of important files with brief descriptions]

### Dependencies
[External libraries, services, or internal modules used]

## Related Work

### Pull Requests
[Link to any related PRs with brief descriptions]

### Linear Tickets
[Link to any related Linear issues]

## Research & References

### Documentation
[Links to relevant official docs]

### Best Practices
[Industry standards and recommendations discovered]

### External Resources
[Helpful articles, tutorials, or examples found]

## Open Questions / Future Work
[Any unresolved questions or potential improvements]

## Appendix
[Additional details, code snippets, or diagrams if needed]
```

### Phase 4: Create the Linear Document

Use the `mcp__plugin_linear_linear__create_document` tool to create the document:

1. **Title**: Clear, descriptive title for the document
2. **Project**: Ask user which Linear project to use (or detect from context)
3. **Content**: The compiled markdown content

## Important Notes

- **Always verify context** - Confirm with the user that you've identified the correct topic
- **Be thorough but concise** - Include important details without overwhelming
- **Link, don't copy** - Reference external resources rather than copying content
- **Structured for scanning** - Use headers, bullets, and formatting for readability
- **Include the PR** - If there's a related PR, always include it prominently

## Example Invocations

- `/linear-research-doc` - Auto-detect topic from recent work
- `/linear-research-doc OAuth implementation` - Create doc about OAuth work
- `/linear-research-doc` after completing a feature - Document the completed work

## Output

After creating the document, provide:
1. Link to the created Linear document
2. Brief summary of what was included
3. Any gaps or areas that might need manual additions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/certivpaul) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
