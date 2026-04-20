---
name: mcp-deepwiki
description: Query public GitHub repos via DeepWiki MCP \u2014 AI-powered answers about architecture, internals, and design decisions without cloning. Use for: 'how does this repo work', 'explain the architecture of X', 'compare two frameworks'. No auth required. Use when this capability is needed.
metadata:
  author: helloworldsungin
---

<objective>
Query public GitHub repositories through the DeepWiki MCP server. Get AI-powered answers about repo architecture, internals, design decisions, and usage patterns — without cloning the repo locally. No authentication required.

**DeepWiki vs Context7 vs GitHub MCP**:
- **DeepWiki**: Architecture, internals, design decisions — "How does X work under the hood?"
- **Context7**: API docs, code examples, usage patterns — "How do I use X?"
- **GitHub MCP**: Source code, PRs, issues, commits — "What does the code say?"
</objective>

<process>

## Investigation Workflow

For understanding an unfamiliar repo, follow this order:

### 1. Browse Structure First

See what documentation topics are available:

```bash
mcporter call 'deepwiki.read_wiki_structure(repoName: "facebook/react")' --output json
```

Returns a table of contents for the repo's wiki. Use this to understand what's documented before asking questions.

### 2. Ask Targeted Questions

Best for specific questions. Returns AI-powered, context-grounded answers:

```bash
mcporter call 'deepwiki.ask_question(repoName: "facebook/react", question: "How does the fiber reconciler work?")' --output json
```

### 3. Read Full Wiki (Use Sparingly)

Gets the complete documentation for a repo. **Warning**: Can return 10K+ tokens. Only use when you need a comprehensive dump:

```bash
mcporter call 'deepwiki.read_wiki_contents(repoName: "facebook/react")' --output json
```

**Prefer `ask_question` for targeted queries** — it's faster and returns only relevant content.

## Multi-Repo Comparison

Compare approaches across repos (max 10):

```bash
mcporter call 'deepwiki.ask_question(repoName: ["vercel/next.js", "remix-run/remix"], question: "How do these frameworks handle server-side rendering differently?")' --output json
```

## Common Patterns

```bash
# Understand a library's architecture
mcporter call 'deepwiki.ask_question(repoName: "steipete/mcporter", question: "How does the config discovery and merging work?")' --output json

# Investigate design decisions
mcporter call 'deepwiki.ask_question(repoName: "anthropics/claude-code", question: "How does the permission system work?")' --output json

# Compare competing libraries
mcporter call 'deepwiki.ask_question(repoName: ["expressjs/express", "fastify/fastify"], question: "How do these handle middleware differently?")' --output json

# Explore what docs exist before deep diving
mcporter call 'deepwiki.read_wiki_structure(repoName: "langchain-ai/langchain")' --output json
```

</process>

<tips>
- **Start with `read_wiki_structure`** to see what's available, then use `ask_question` for specifics.
- **`ask_question` is your primary tool** — it returns focused, relevant answers without the token cost of a full wiki dump.
- **`read_wiki_contents` is expensive** — it returns the entire wiki (often 10K+ tokens). Only use it when you need comprehensive coverage.
- The `repoName` format is always `owner/repo` (e.g., "facebook/react").
- For multiple repos, pass an array (max 10): `repoName: ["repo1", "repo2"]`
- No authentication required — works out of the box.
- Best for **public repos only** — private repos won't be accessible.
- Use `--output json` for machine-readable results.
</tips>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/helloworldsungin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
