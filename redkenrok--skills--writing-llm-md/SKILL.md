---
name: writing-llm-md
description: Teaches how to write effective LLM.md files - condensed, practical reference documents that give AI assistants the context they need to use a library effectively without searching through full documentation. Use when creating library documentation for AI consumption. Use when this capability is needed.
metadata:
  author: redkenrok
---

# Writing LLM.md

## Purpose
An LLM.md is a condensed, practical reference that gives AI assistants just enough context to use a library effectively without wading through full documentation. It prioritizes common patterns over exhaustive API coverage.

## When to use this skill
- Creating documentation for a library or framework
- Writing context files for AI-assisted development
- Summarizing existing documentation for LLM consumption
- Any time you need to provide library context to AI assistants efficiently

## File structure
Place LLM.md at the library root directory:

## Core sections

### 1. TL;DR (Required)
Lead with what matters most. Include:
- One-sentence description of what the library does
- Primary use case
- Typical code snippet showing basic usage

Example:
```markdown
## TL;DR
A promise-based HTTP client for browser and Node.js. Use when you need to make HTTP requests with automatic transforms and interceptors.

```javascript
import axios from 'axios';
const response = await axios.get('/api/users');
```
```

### 2. API at a glance
List the functions, but not every detail. Include:
- Function signatures with types
- Brief return value description
- One-line purpose
- Note on any gotchas

Example:
```markdown
## API at a Glance

| Function | Returns | Purpose |
|----------|---------|---------|
| `axios.get(url, config?)` | `Promise<Response>` | GET request with optional config |
| `axios.post(url, data?, config?)` | `Promise<Response>` | POST with automatic JSON serialization |
| `axios.create(config)` | `AxiosInstance` | Create instance with default config |
| `axios.interceptors` | `InterceptorManager` | Add request/response middleware |

Config options:
- `baseURL`: Prefix for all requests
- `headers`: Default headers
- `timeout`: Request timeout in ms (default: 0 = no timeout)
- `params`: Query parameters (auto-encoded)
```

### 3. Context LLMs Often Miss
Include implicit behaviors, defaults, and gotchas that trip up AI assistants:

```markdown
## Implicit Behaviors

- Auto-JSON: Requests with object `data` are JSON-encoded automatically; responses with `Content-Type: application/json` are parsed automatically
- Default timeout: No timeout by default (`timeout: 0`)
- Error handling: Non-2xx responses throw; check `error.response.status`
- BaseURL merging: Relative URLs are resolved against `baseURL` using standard URL resolution rules
- Header casing: Headers are lowercased internally; don't rely on casing for logic
```

### 4. Anti-patterns
Show common mistakes and the correct approach:

```markdown
## Anti-patterns

Don't manually stringify JSON:
```javascript
axios.post('/users', JSON.stringify(user), {
  headers: { 'Content-Type': 'application/json' }
});
```

Let axios handle serialization:
```javascript
axios.post('/users', user); // Automatically stringified
```
```

## Writing principles

### Frontload examples
AI assistants pattern-match on code. Put working examples before explanations.

### Be opinionated
State the "right way" clearly. Don't present 5 equivalent options, pick the common recommended path.

### Use decision trees
```markdown
Choosing between sync and async:
- Use async/await for I/O bound operations (network, disk)
- Use sync methods only for in-memory operations with known-small data
- Never use sync methods in request handlers
```

### Link, don't duplicate
Reference full documentation for edge cases:
```markdown
For advanced interceptors, custom adapters, and request cancellation, see [Full Documentation](./docs/advanced.md).
```

### Include real-world context
Mention when NOT to use the library:
```markdown
## When NOT to use
- For simple static file serving, use `python -m http.server`
- For production reverse proxy, use nginx
- For WebSocket communication, use native WebSocket or Socket.io
```

## Formatting guidelines

- Code blocks: Always specify language for syntax highlighting
- Tables: Use for API summaries and option references

## Review checklist
Before finalizing an LLM.md:

- [ ] TL;DR section has working code example
- [ ] Common patterns cover 80% of real-world usage
- [ ] API at a glance lists possbiel functions
- [ ] Implicit behaviours and defaults are documented
- [ ] Any anti-patterns with corrections are shown
- [ ] Full documentation is linked for deep dives
- [ ] Examples are copy-pasteable and runnable

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/redkenrok) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
