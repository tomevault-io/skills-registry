---
name: sse-ai-optimization
description: Use AI to accelerate SSE development. Includes prompt templates for code generation, refactoring, testing, and architectural decisions. Use when this capability is needed.
metadata:
  author: vuthuonghai-steve
---

# SSE AI Optimization

Leverage AI to reduce SSE development time by 70%: Generate, debug, test, and optimize SSE code.

## Why AI for SSE Development?

SSE development involves repetitive patterns:
- Route handlers (TransformStream, heartbeat, cleanup)
- Client components (EventSource, error handling, reconnect)
- Integration code (hooks, pub/sub, batching)

AI can:
- **Generate boilerplate** in seconds (vs 10-15 min manual)
- **Suggest optimizations** based on best practices
- **Debug issues** by analyzing code patterns
- **Write tests** covering edge cases
- **Explain trade-offs** (SSE vs WebSockets vs polling)

## AI Prompts by Task

### 1. Generate Basic SSE Endpoint

**Prompt Template**:
```
Generate a basic SSE endpoint in Next.js App Router that:
- Streams notifications to authenticated users
- Sends a heartbeat every 30 seconds
- Includes proper error handling and cleanup
- Uses [TransformStream / ReadableStream]
```

**Example**:
```
Generate a basic SSE endpoint in Next.js App Router for a social app that streams notifications.
Include JWT auth verification, heartbeat every 30 seconds, and cleanup on client disconnect.
```

**Expected output**: Complete route handler with:
- Proper headers (text/event-stream, Cache-Control, Connection)
- Authentication middleware
- Heartbeat interval
- Request abort handling

### 2. Integrate with Payload CMS

**Prompt Template**:
```
Create a Payload CMS afterChange hook that:
- Detects when a [Post/Comment/Like] is [created/updated]
- Triggers SSE notification to [specific user / all followers]
- Uses [EventEmitter / Redis pub/sub] for delivery
```

**Example**:
```
Show me how to integrate Payload CMS afterChange hook with SSE.
When a post gets liked, notify the post author via SSE using Redis pub/sub.
```

**Expected output**: Hook code with:
- Condition checking (likes count increased)
- Redis publish call
- Error handling
- Logging

### 3. Scale to Multiple Instances

**Prompt Template**:
```
Refactor my SSE code to use Redis pub/sub for:
- Multi-instance server deployment
- Handling [100+ / 1000+] concurrent users
- Broadcasting to user segments
```

**Example**:
```
Refactor my EventEmitter-based SSE to use Redis pub/sub.
I have multiple server instances and need to broadcast notifications
across all instances to 500+ concurrent users. Show connection pooling.
```

**Expected output**:
- Redis client setup
- Subscribe/publish code
- Connection pooling
- Cleanup on disconnect

### 4. Add Error Handling & Reconnection

**Prompt Template**:
```
Add client-side error handling to my SSE component:
- Implement exponential backoff (1s, 2s, 4s, 8s, 16s)
- Max retry limit of [5/10] attempts
- Display connection status to user
- Log errors for debugging
```

**Example**:
```
My SSE client keeps losing connection. Add robust error handling with:
- Exponential backoff reconnection
- Visual status indicator (connected/disconnected)
- Max 5 retry attempts
- Detailed error logging
```

**Expected output**: React component with:
- onerror handler
- Exponential backoff logic
- Status state management
- Error messages

### 5. Write Tests

**Prompt Template**:
```
Write [Jest/Vitest] tests for my SSE endpoint:
- Test [successful connection / message streaming / client disconnect]
- Mock [fetch / Response / ReadableStream]
- Test [error scenarios / timeout]
- Measure latency
```

**Example**:
```
Write Jest tests for my SSE route handler. Test:
1. Client connects and receives initial message
2. Heartbeat message sent every 30s
3. Client disconnect closes stream
4. Error on invalid auth token
```

**Expected output**: Test suite with:
- Mock setup
- Connection test
- Message streaming test
- Error test
- Cleanup test

### 6. Optimize Performance

**Prompt Template**:
```
Optimize my SSE for [1000 / 10000] concurrent users:
- Current bottleneck: [memory / latency / message throughput]
- Show [batching / connection pooling / selective delivery] approach
- Estimate resource usage
```

**Example**:
```
My SSE is hitting memory limits at 1000 concurrent users.
Suggest optimizations using:
- Message batching (100ms window)
- Selective delivery (only send to interested users)
- Memory pooling
Show estimated resource usage and latency impact.
```

**Expected output**:
- Optimization strategies
- Code examples
- Resource estimates
- Latency analysis

### 7. Compare Architecture Choices

**Prompt Template**:
```
Compare SSE vs [WebSockets / Long Polling / Polling] for my use case:
- Features needed: [realtime / bidirectional / fallback]
- Scale: [10 / 100 / 1000 / 10000] users
- Latency requirement: [<100ms / <500ms / <1s]
```

**Example**:
```
Should I use SSE or WebSockets for my social feed?
- One-way: Server sends posts, users only "like" via HTTP
- Scale: Expected 500-1000 concurrent users
- Latency: <200ms acceptable
Show pros/cons and recommendation.
```

**Expected output**:
- Comparison table
- Pros/cons analysis
- Recommendation
- Implementation complexity

## Workflow: AI-Driven Development

### Phase 1: Design (30 min)
1. **Prompt**: "Design SSE architecture for [use case]"
2. AI generates diagrams, data flow, scaling strategy

### Phase 2: Implementation (1-2 hours)
1. **Prompt**: "Generate SSE endpoint + client component + Payload hook"
2. AI generates working code (with templates from sse-basics, sse-scaling skills)
3. Copy templates and customize

### Phase 3: Testing (30 min)
1. **Prompt**: "Write Jest tests for my SSE endpoint"
2. AI generates test suite
3. Run tests and fix failures

### Phase 4: Optimization (30 min)
1. **Prompt**: "Optimize my SSE for [requirements]"
2. AI suggests improvements
3. Implement and measure

**Total**: 2.5-3 hours vs 6-8 hours manual (70% time savings)

## Chain AI Agents

Combine multiple prompts in sequence:

```
1. Generate SSE endpoint
   ↓
2. Generate Payload hook
   ↓
3. Generate React component
   ↓
4. Generate tests
   ↓
5. Optimize for scale
```

Each output feeds into next prompt.

## Debugging with AI

**Prompt for buggy SSE**:
```
Debug this SSE endpoint - messages buffer until connection closes:
[paste code]

Why is this happening? How do I fix it?
```

**Expected**: Diagnosis (Vercel buffering) + fix (TransformStream)

**Prompt for errors**:
```
My SSE client throws "Failed to parse event.data" occasionally.
[paste error + code]

What's wrong? How do I fix the parsing?
```

**Expected**: Identify cause (invalid JSON) + solution (add try-catch)

## Best Prompting Practices

1. **Be specific**: Include framework, scale, constraints
2. **Show context**: Paste existing code for optimization
3. **Provide examples**: "Like this: [example]"
4. **Ask for explanations**: "Explain why..." for learning
5. **Request variations**: "Also show Redis version"

## Limitations & Guardrails

- AI may generate working but not optimal code → verify with tests
- Templates should match your project structure → adjust imports
- AI doesn't know your business logic → customize data handling
- Error handling may be incomplete → add domain-specific logic

## Resources

- `sse-basics` skill - Use templates from here
- `sse-debugging` skill - Debugging strategies
- `sse-scaling` skill - Scaling patterns
- `sse-payload-integration` skill - CMS integration
- `Docs/life-1/03-research/sse-nextjs.md` - Prototype code

## Example: Full Development with AI

**Time: 3 hours (vs 8 hours manual)**

1. (10 min) Prompt: "Generate SSE architecture for social feed notifications"
2. (20 min) Prompt: "Generate basic SSE endpoint in Next.js"
3. (15 min) Prompt: "Generate Payload hook to trigger SSE on like/comment"
4. (20 min) Prompt: "Generate React component with reconnection"
5. (20 min) Prompt: "Write Jest tests for SSE endpoint"
6. (20 min) Prompt: "Optimize SSE for 1000 concurrent users with Redis"
7. (15 min) Testing, debugging, final optimizations

Result: Production-ready SSE system with tests, docs, and optimizations.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vuthuonghai-steve) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
