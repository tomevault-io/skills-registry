---
name: check-third-party-docs
description: Esta skill debe usarse cuando el usuario pide \"investiga documentación de\", \"consulta documentación de\", \"quiero integrar X en\", \"cómo integrar\", \"implementar X en proyecto\", \"qué librería para\", \"instalar\", \"configurar\", \"error con\", o menciona agregar funcionalidad que requiere dependencias especializadas (validación con zod, procesamiento imágenes con sharp, colas con bull, caché con ioredis, formularios con react-hook-form). IMPORTANTE: Invocar esta skill en lugar de usar Context7 MCP directamente - la skill proporciona framework de decisión sobre cuándo usar Context7 vs dependency-docs-collector agent según complejidad. No usar para paquetes ampliamente conocidos como express, react, vue, lodash, axios, jest; Claude tiene datos de entrenamiento suficientes para estos. Usar solo para paquetes especializados, plugins de framework, o librerías poco conocidas. Use when this capability is needed.
metadata:
  author: diegopherlt
---

# Third-Party Package Documentation Workflow

Provides decision framework for accessing documentation on specialized third-party packages using Context7 MCP tools and dependency-docs-collector agent.

## Decision Framework

### Use Context7 MCP Directly

For **quick, focused lookups** on specialized packages when you already know what to ask:

**Scenarios:**

- API syntax verification during coding
  - "Verify `zod.object()` schema syntax"
  - "Check `sharp.resize()` options"
- Single method/function lookup
  - "What are `bull` queue retry options?"
  - "How to use `react-hook-form` controller?"
- Specific "how to" for known package
  - "How to validate nested objects with `yup`?"
  - "How to pipeline commands in `ioredis`?"

**Usage:**

```typescript
// 1. Resolve package to Context7 library ID
resolve-library-id("zod", "validate nested objects with zod")

// 2. Query specific documentation
query-docs("/colinhacks/zod", "How to validate nested objects with zod schemas")
```

**When Context7 fails:**

- Package not in Context7 index → Fall back to dependency-docs-collector agent
- After 3 failed resolve attempts → Use agent for web documentation search
- Outdated version docs → Agent can fetch latest version-specific docs

### Use dependency-docs-collector Agent

For **comprehensive documentation gathering** when you need implementation guidance:

**Scenarios:**

#### 1. Adding New Library for Feature

User wants to implement a feature requiring a new dependency:

```
User: "I need to add background job processing to my Express API"
→ Agent: Research job queue libraries (bull, agenda, bee-queue)
→ Gather installation, Redis setup, queue configuration, worker patterns
→ Provide implementation plan with chosen library

User: "Add PDF generation to my Next.js app"
→ Agent: Fetch pdfkit/puppeteer docs
→ Installation, API usage, Next.js integration patterns
→ Example implementation with serverless considerations
```

#### 2. Migrating Between Libraries

User wants to replace existing library with alternative:

```
User: "Migrate from joi to zod for validation"
→ Agent: Gather migration guide, breaking changes
→ Pattern conversions (joi schemas → zod schemas)
→ API differences, TypeScript integration improvements
→ Step-by-step migration plan

User: "Switch from moment to date-fns"
→ Agent: Migration documentation, bundle size comparison
→ Method mapping (moment.format() → date-fns format())
→ Timezone handling differences
```

#### 3. Finding Alternative to Existing Library

User needs replacement due to deprecation, performance, or features:

```
User: "Need alternative to deprecated request library"
→ Agent: Research modern alternatives (axios, got, node-fetch)
→ Feature comparison, API differences
→ Migration guide for chosen alternative

User: "Looking for lighter alternative to lodash"
→ Agent: Evaluate alternatives (just-*, native JS methods)
→ Bundle size analysis, feature parity check
→ Migration recommendations
```

#### 4. Troubleshooting Package Errors

User encounters errors with specialized dependencies:

```
User: "Getting 'ZodError: Invalid input' with zod validation"
→ Agent: Fetch zod error handling docs
→ Common validation error patterns
→ Debugging techniques, schema refinement

User: "Sharp image processing throwing 'unsupported image format'"
→ Agent: Gather sharp supported formats, installation issues
→ libvips troubleshooting, platform-specific fixes
```

#### 5. Complex Multi-Package Setup

User needs to configure multiple related packages:

```
User: "Set up authentication with next-auth, prisma, and zod"
→ Agent: Gather docs for all three packages
→ Integration patterns, adapter configuration
→ Schema validation with auth flows
→ Complete setup guide

User: "Configure testing with Jest, React Testing Library, MSW"
→ Agent: Fetch setup docs for each package
→ Integration configuration, common patterns
→ Example test suite structure
```

**Agent Invocation:**

```typescript
Task({
  subagent_type: "dotclaudefiles:dependency-docs-collector",
  prompt: `
    User wants to [add library X for Y feature / migrate from A to B / find alternative to C].
    Context: [language, framework, specific problem]
    Error (if troubleshooting): [error message]

    Gather documentation, provide implementation/migration plan.
  `
})
```

## Context7 + Troubleshooting Workflow

When troubleshooting with Context7:

1. **Error analysis**: Identify package causing error
2. **Quick lookup**: Use Context7 for error message/API verification
3. **If Context7 insufficient**: Escalate to agent for comprehensive debugging

**Example:**

```
User: "Getting error: 'Queue job failed with status 500' in bull"

Step 1: Quick Context7 lookup
→ query-docs("bull", "job failure handling error status codes")
→ Find basic error handling patterns

Step 2: If error persists or needs deeper investigation
→ Escalate to dependency-docs-collector agent
→ Agent gathers: error handling docs, retry strategies, logging patterns
→ Provides comprehensive debugging guide
```

## Critical Constraints

### NEVER Use Context7 for Standard Libraries

Context7 is **only** for third-party packages:

❌ **Do NOT use:**

- Language standard libraries: `fmt.Println` (Go), `Array.map` (JS), `os.ReadFile` (Go)
- Built-in features: `Promise`, `async/await`, Python list comprehensions
- Platform APIs: `document.querySelector`, `fetch`, `localStorage`

✅ **Use for:**

- Third-party packages: `zod`, `sharp`, `bull`, `ioredis`, `react-hook-form`
- Framework plugins: `next-pwa`, `@auth/core`, `prisma` adapters
- Specialized libraries: `pdfkit`, `winston` transports, `yup`

## Quick Reference

### Context7 Tools

**resolve-library-id(libraryName, query)**

- Converts package name → Context7 library ID
- Max 3 calls per question

**query-docs(libraryId, query)**

- Retrieves documentation for specific query
- Max 3 calls per question

### Agent

**dependency-docs-collector**

- Task tool: `subagent_type: "dotclaudefiles:dependency-docs-collector"`
- Use for: New features, migrations, alternatives, troubleshooting, multi-package setups

## Common Patterns Summary

| Scenario | Tool | Example |
|----------|------|---------|
| Quick API lookup | Context7 | "Verify zod schema syntax" |
| Add library for feature | Agent | "Add job queue for background processing" |
| Migrate library | Agent | "Migrate joi to zod" |
| Find alternative | Agent | "Alternative to deprecated request library" |
| Troubleshoot error (simple) | Context7 | "Bull job status codes" |
| Troubleshoot error (complex) | Agent | "Debug zod validation failures" |
| Multi-package setup | Agent | "Configure next-auth + prisma + zod" |

---

**Version:** 0.1.0
**Plugin:** dotclaudefiles

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/diegopherlt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
