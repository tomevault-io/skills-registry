---
name: mcp-context7
description: Look up library and framework documentation via Context7 MCP. Use for: 'how do I use React hooks', 'FastAPI middleware docs', 'pandas merge examples', 'Next.js app router API reference'. No auth required. Use when this capability is needed.
metadata:
  author: helloworldsungin
---

<objective>
Query up-to-date documentation and code examples for any programming library or framework via the Context7 MCP server. Best for API references, code examples, and usage patterns. No authentication required. Two-step process: resolve the library ID, then query docs.

**Context7 vs DeepWiki**: Use Context7 for API docs, code examples, and "how do I use X?" questions. Use DeepWiki for architecture, internals, and "how does X work under the hood?" questions.
</objective>

<process>

## Step 1: Resolve Library ID

Find the Context7 library ID for the library you need:

```bash
mcporter call 'context7.resolve-library-id(query: "how to use React hooks", libraryName: "react")' --output json
```

Returns matching libraries ranked by relevance. Pick the `libraryId` from the best match (format: `/org/project`).

**Skip this step** if you already know the library ID from the list below.

## Common Library IDs

| Library | ID | Library | ID |
|---------|----|---------|----|
| React | `/facebook/react` | Next.js | `/vercel/next.js` |
| TypeScript | `/microsoft/TypeScript` | Tailwind CSS | `/tailwindlabs/tailwindcss` |
| Node.js | `/nodejs/node` | Express | `/expressjs/express` |
| Vue | `/vuejs/vue` | Svelte | `/sveltejs/svelte` |
| Angular | `/angular/angular` | Remix | `/remix-run/remix` |
| Python (pandas) | `/pandas-dev/pandas` | scikit-learn | `/scikit-learn/scikit-learn` |
| Flask | `/pallets/flask` | Django | `/django/django` |
| FastAPI | `/tiangolo/fastapi` | SQLAlchemy | `/sqlalchemy/sqlalchemy` |
| LangChain | `/langchain-ai/langchain` | Anthropic SDK | `/anthropics/anthropic-sdk-python` |
| Rust | `/rust-lang/rust` | Go | `/golang/go` |
| PostgreSQL | `/postgres/postgres` | Redis | `/redis/redis` |
| Docker | `/docker/docs` | Kubernetes | `/kubernetes/kubernetes` |

## Step 2: Query Documentation

```bash
mcporter call 'context7.query-docs(libraryId: "/vercel/next.js", query: "how to set up middleware for authentication")' --output json
```

Response includes: `libraryId`, `title`, `description`, `codeSnippetCount`, and the documentation content.

## Examples

```bash
# React hooks (known ID — skip resolve step)
mcporter call 'context7.query-docs(libraryId: "/facebook/react", query: "useEffect cleanup patterns")' --output json

# Next.js routing (known ID)
mcporter call 'context7.query-docs(libraryId: "/vercel/next.js", query: "app router dynamic routes with params")' --output json

# Python library (known ID)
mcporter call 'context7.query-docs(libraryId: "/pandas-dev/pandas", query: "merge dataframes on multiple columns")' --output json

# Unknown library — resolve first
mcporter call 'context7.resolve-library-id(query: "Prisma ORM", libraryName: "prisma")' --output json
mcporter call 'context7.query-docs(libraryId: "/prisma/prisma", query: "define relations between models")' --output json
```

</process>

<tips>
- **Skip resolve** for libraries in the common IDs table above — go directly to `query-docs`.
- Do not call either tool more than 3 times per question. Use the best result you have.
- No authentication required — this server works out of the box.
- **Write queries as questions**, not keywords. "How do I handle form validation in React?" works better than "React form validation".
- Include use case context in your query for better results: "useEffect with async fetch and cleanup" > "useEffect".
- If resolve returns no results, try a different `libraryName` or a more descriptive `query`.
- Use `--output json` to parse results programmatically.
</tips>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/helloworldsungin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
