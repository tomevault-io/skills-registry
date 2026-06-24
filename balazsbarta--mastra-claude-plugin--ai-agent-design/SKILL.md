---
name: ai-agent-design
description: > Use when this capability is needed.
metadata:
  author: balazsbarta
---

# AI Agent Design

How to design AI agents that actually work. This skill covers the craft of agent design independent of any specific framework — the principles apply to Mastra and any other agent system.

> **Note on code examples:** The design principles in this skill are stable and framework-agnostic, but any Mastra-specific code examples (model names, constructor params, `maxSteps` values) are illustrative. Before using them, verify current API signatures via the `mastra` skill's documentation lookup — check embedded docs first (`node_modules/@mastra/*/dist/docs/`), then fall back to remote docs (`https://mastra.ai/llms.txt`). Never rely on training data for Mastra API details.

## Writing Effective Agent Instructions

Agent instructions (system prompts) are the single most important factor in agent quality. They define the agent's identity, boundaries, and decision-making process.

### The Instruction Hierarchy

Structure instructions from most to least important:

1. **Identity & role** — Who is this agent? What domain does it operate in?
2. **Boundaries** — What should it NOT do? When should it refuse or escalate?
3. **Tool usage guidance** — When to use which tool, in what order
4. **Output format** — How to structure responses
5. **Edge cases** — What to do when uncertain, when data is missing, etc.

### Writing Principles

**Be specific, not vague:**
```
BAD:  "You are a helpful assistant."
GOOD: "You are a customer support agent for an e-commerce platform.
       You help users with order tracking, returns, and product questions.
       You do NOT handle billing disputes — escalate those to the billing team."
```

**Name your tools and explain when to use them:**
```
BAD:  "Use the available tools to help the user."
GOOD: "You have access to these tools:
       - orderLookupTool: Use when the user asks about an order status or needs order details. Requires an order ID or customer email.
       - returnTool: Use to initiate a return. Only use after confirming the order exists via orderLookupTool.
       - faqSearchTool: Use for general product questions before crafting your own answer."
```

**Define behavior at boundaries:**
```
"If the user asks about something outside your scope (pricing changes, account deletion, legal matters):
 1. Acknowledge their request
 2. Explain you can't help with that specific topic
 3. Suggest who can help (billing team, account management, legal@company.com)"
```

**Include examples for ambiguous situations:**
```
"When a user complains about a late delivery:
 1. Look up the order first
 2. Check the current shipping status
 3. If the package is in transit, provide the tracking info and expected date
 4. If the package is lost (no updates for 5+ days), offer a replacement or refund
 5. Always apologize for the inconvenience regardless of the cause"
```

### Dynamic Instructions

Use dynamic instructions (async functions) when:
- Instructions change based on user role, subscription tier, or context
- You want to A/B test different instruction variants
- Instructions reference data that changes frequently (feature flags, policy documents)

Do NOT use dynamic instructions when:
- Instructions are static and universal
- The overhead of fetching instructions adds latency without value

## Tool Design

Tools are the hands and eyes of an agent. Poorly designed tools cause agents to fail even with perfect instructions.

### The Tool Design Checklist

1. **One tool, one job.** A tool should do exactly one thing. If you're tempted to add a "mode" parameter, make two tools instead.

2. **Descriptions are for the LLM.** Write the description as if explaining to a smart colleague what this tool does and when to use it. Be specific about inputs and expected outcomes.

3. **Schema fields are self-documenting.** Use descriptive names and `.describe()` on every field:
   ```typescript
   // BAD
   z.object({ q: z.string(), n: z.number() })

   // GOOD
   z.object({
     query: z.string().describe("The search query to execute"),
     maxResults: z.number().default(10).describe("Maximum number of results to return (1-100)"),
   })
   ```

4. **Constrain inputs.** Use enums, min/max, regex patterns to prevent invalid inputs:
   ```typescript
   z.object({
     priority: z.enum(["low", "medium", "high"]).describe("Ticket priority level"),
     daysBack: z.number().min(1).max(90).describe("Number of days to search back"),
   })
   ```

5. **Return structured errors.** Never let tools throw unhandled exceptions — return error information the agent can reason about:
   ```typescript
   execute: async (input) => {
     try {
       const data = await fetchData(input);
       return { success: true, data, error: null };
     } catch (e) {
       return { success: false, data: null, error: `Failed to fetch: ${e.message}` };
     }
   }
   ```

6. **Minimize output size.** Don't return entire database rows when the agent only needs two fields. Large outputs waste tokens and confuse the model.

### Tool Granularity Spectrum

```
Too granular:                          Too coarse:
getUser, getUserEmail,                 doEverything(action, params)
getUserName, getUserOrders...

Sweet spot:
getUser(userId) → { name, email, role }
getUserOrders(userId, { limit, status }) → Order[]
createOrder(userId, items) → Order
```

### When NOT to Use Tools

Not everything needs to be a tool. The LLM can:
- Format and restructure data it already has
- Do math on small numbers
- Generate text, summaries, translations
- Make decisions based on provided context

Use tools for things the LLM can't do: fetch live data, execute code, interact with external systems, perform precise calculations.

## Model Selection Strategy

### Matching Model to Task

| Task characteristics | Recommended tier | Examples |
|---------------------|-----------------|----------|
| Simple routing, classification, extraction | Small/fast (`gpt-4o-mini`, `haiku`) | Ticket classification, entity extraction |
| General reasoning, tool use, conversation | Mid-tier (`gpt-4o`, `sonnet`) | Customer support, research, analysis |
| Complex reasoning, nuanced judgment, coding | Large (`o1/o3`, `opus`) | Architecture design, code review, legal analysis |

### Cost Optimization Patterns

1. **Tiered model selection:** Use cheap models for easy tasks, expensive ones for hard tasks. In agent networks, the routing agent can use a cheaper model while specialist agents use more capable ones.

2. **Minimize context:** Don't send the agent's entire conversation history when it only needs the last message. Use `lastMessages` judiciously.

3. **Limit `maxSteps`:** Set reasonable limits to prevent runaway tool loops. Start with 3-5 and increase only if agents consistently need more.

4. **Cache tool results:** If a tool fetches slowly-changing data, cache it rather than re-fetching every turn.

5. **Structured output over parsing:** Using schema-based structured output is cheaper and more reliable than asking the model to format JSON in its response.

## Evaluation & Reliability

### What to Measure

| Metric | What it tells you | How to measure |
|--------|------------------|----------------|
| Task completion | Does the agent achieve the goal? | Manual review, automated checks |
| Tool accuracy | Does it call the right tools with right inputs? | Log tool calls, compare to expected |
| Hallucination rate | Does it make things up? | Compare claims against ground truth |
| Latency | How long does a response take? | End-to-end timing |
| Token usage | How much does each interaction cost? | Sum input + output tokens per turn |
| User satisfaction | Do users find it helpful? | Thumbs up/down, follow-up questions |

### Guardrails

**Input guardrails** (before the agent processes):
- Validate user input format and length
- Detect prompt injection attempts
- Filter out-of-scope requests early

**Output guardrails** (before the response reaches the user):
- Check for PII in responses
- Validate factual claims against tools
- Ensure response format matches requirements

**Execution guardrails** (during agent processing):
- `maxSteps` to prevent infinite loops
- Timeout limits on tool execution
- Budget caps on token usage per session

### The Testing Pyramid for Agents

```
          /  Manual testing  \        ← Exploratory, edge cases
         /  Integration tests \       ← Full agent + tools + memory
        /    Agent eval suites \      ← Automated scoring on test cases
       /      Tool unit tests   \     ← Inputs → outputs for each tool
      / Schema validation tests  \    ← Schema edge cases, types
```

## Resources

- **Tool design patterns**: `references/tool-design-patterns.md`
- **Instruction templates**: `references/instruction-templates.md`

---
> Source: [balazsbarta/mastra-claude-plugin](https://github.com/balazsbarta/mastra-claude-plugin) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
