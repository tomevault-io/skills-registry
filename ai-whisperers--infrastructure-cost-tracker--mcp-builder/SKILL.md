---
name: mcp-builder
description: Build Model Context Protocol (MCP) servers to extend OpenClaw with custom tools and integrations. Use when creating new skills, connecting external APIs, or building agent-to-agent protocols. Use when this capability is needed.
metadata:
  author: ai-whisperers
---

# MCP Builder Skill

Build Model Context Protocol (MCP) servers to extend OpenClaw with custom tools and integrations.

## When to Use

- Creating new skills for OpenClaw
- Connecting external APIs and services
- Building agent-to-agent communication protocols
- Extending AI capabilities with custom tools

## Quick Start

Create a new MCP server:

```bash
npx @anthropic-ai/mcp init my-skill
cd my-skill
npm install
```

## Core Concepts

### Tools
Tools are functions that the AI can call:

```typescript
server.tool("search_web", {
  query: z.string()
}, async ({ query }) => {
  const results = await search(query);
  return { content: [{ type: "text", text: results }] };
});
```

### Resources
Resources provide data to the AI:

```typescript
server.resource("docs", "docs://{path}", async (uri, { path }) => {
  const content = await readFile(path);
  return { contents: [{ uri: uri.href, mimeType: "text/markdown", text: content }] };
});
```

## Best Practices

1. Always validate inputs with Zod schemas
2. Return structured, parseable responses
3. Handle errors gracefully
4. Document all tools and resources
5. Test thoroughly before deployment

## Examples

### Weather API Tool

```typescript
server.tool("get_weather", {
  location: z.string(),
  units: z.enum(["celsius", "fahrenheit"]).default("celsius")
}, async ({ location, units }) => {
  const weather = await fetchWeather(location, units);
  return {
    content: [{
      type: "text",
      text: `Weather in ${location}: ${weather.temperature}°${units === 'celsius' ? 'C' : 'F'}, ${weather.condition}`
    }]
  };
});
```

### File Resource

```typescript
server.resource("config", "config://app", async (uri) => {
  const config = await readConfig();
  return {
    contents: [{
      uri: uri.href,
      mimeType: "application/json",
      text: JSON.stringify(config, null, 2)
    }]
  };
});
```

## Deployment

1. Test locally with `mcp dev`
2. Build for production: `npm run build`
3. Deploy to your infrastructure
4. Configure in OpenClaw: `openclaw config set skills.entries.my-skill.enabled true`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ai-whisperers) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
