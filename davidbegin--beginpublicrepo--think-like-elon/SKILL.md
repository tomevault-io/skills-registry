---
name: think-like-elon
description: Apply first principles thinking and aggressive optimization strategies to engineering problems. Use this skill when tackling complex technical challenges, architectural decisions, performance optimization, cost reduction, or when the user explicitly asks to "think like Elon" or apply first principles reasoning. Use when this capability is needed.
metadata:
  author: davidbegin
---

This skill guides engineering problem-solving through first principles thinking, radical simplification, and aggressive optimization - principles demonstrated by Elon Musk across SpaceX, Tesla, and other ventures.

## Core Principles

### 1. First Principles Thinking

Break problems down to fundamental truths and reason up from there:

- **Question Every Assumption**: Challenge inherited wisdom. Ask "Why?" five times.
- **Identify Core Constraints**: What are the actual physical/mathematical limits? Most "constraints" are just legacy decisions.
- **Rebuild from Scratch**: Once you reach bedrock truths, rebuild the solution without inherited baggage.

**Example**: Don't accept "databases are expensive" - ask what you actually need (durable storage + queries), then find the cheapest physics-based solution (SQLite in Durable Objects vs. managed DB).

### 2. Aggressive Simplification

Complexity is a tax on everything:

- **Delete Before Optimize**: The best code is no code. Remove features, remove layers, remove abstractions.
- **Question Every Dependency**: Each dependency is a future liability. Can you build it simpler yourself?
- **Vertical Integration When It Matters**: Own the stack when the abstraction leaks or costs too much.

**Rule**: If you can't explain why something exists in one sentence, delete it.

### 3. Optimize the Right Thing

Speed and cost are features:

- **Speed**: Every millisecond matters. Users notice 100ms. Aim for <50ms API responses.
- **Cost**: Engineer for 10x cost reduction, not 10% improvement. Question every paid service.
- **Reliability**: Build systems that work, then make them fast. Broken fast code is just broken.

### 4. Manufacturing Mindset

Software is manufacturing at the speed of thought:

- **Delete the Step**: Can this entire process be removed? (Best option)
- **Simplify the Step**: Can it be done with less code/complexity?
- **Optimize the Step**: Only after 1 & 2, make it faster.
- **Automate the Step**: Last resort - automation locks in complexity.

### 5. Idiot Index

Calculate the "Idiot Index" - ratio of total cost to raw material cost:

- **Infrastructure**: Are you paying 10x for managed services vs. raw compute?
- **Dependencies**: Could you build this critical package in 100 lines instead of importing 10MB?
- **Processes**: Are manual processes costing more than building automation?

**Target**: Keep Idiot Index under 2x for critical paths.

## Application Patterns

### Architecture Review

When evaluating system design:

1. **What are we actually trying to do?** (First principles)
2. **What's the simplest possible implementation?** (Remove everything)
3. **What are the real bottlenecks?** (Measure, don't guess)
4. **Can we delete this entire subsystem?** (Aggressive simplification)
5. **What would 10x scale look like?** (Avoid rework)

### Performance Optimization

1. **Measure first**: Profile, don't assume. Numbers are truth.
2. **Delete slow code**: Can the feature be removed or simplified?
3. **Algorithm before infrastructure**: O(n²) → O(n log n) beats adding servers.
4. **Physics-based limits**: What's the theoretical minimum latency? How far are we?
5. **Incremental execution**: Don't wait for perfect - ship fast, measure, iterate.

### Cost Optimization

1. **Question every bill**: Challenge every line item. Why do we pay this?
2. **Raw material cost**: What's the actual compute/storage/bandwidth cost?
3. **Build vs. buy**: For critical paths, custom-built often costs 10x less.
4. **Edge over cloud**: Push computation to the edge when possible (Cloudflare Workers vs. EC2).
5. **Serverless-first**: Pay for execution, not idle capacity.

### Dependency Decisions

Before adding a dependency:

1. **Can I build this in 100 lines?** (If yes, do it)
2. **Is this core to our value prop?** (If yes, own it)
3. **What's the worst-case failure mode?** (Supply chain risk)
4. **What's the total cost of ownership?** (Bundle size + maintenance + updates)
5. **Can I vendor and modify it?** (Escape hatch)

## Security Through Simplicity

**Key Insight**: Simple systems have fewer attack surfaces.

- **Less code = fewer bugs**: Deleted code never breaks.
- **Fewer dependencies = smaller supply chain**: Each package is a risk.
- **Simpler auth = more auditable**: OAuth2 with cookies > complex JWT schemes.
- **Edge deployment = smaller blast radius**: Isolated functions > monolith.

**Mental Model**: Every abstraction layer is a place bugs hide. Shallow stacks are more secure.

## Anti-Patterns to Avoid

- **Premature abstraction**: Don't build frameworks before you have 3+ use cases.
- **Resume-driven development**: Choose boring tech that works, not what's trendy.
- **Analysis paralysis**: Ship something, measure, iterate. Perfect is the enemy of good enough.
- **Optimization theater**: Making things 2% faster that don't matter. Optimize bottlenecks.
- **Inherited wisdom**: "This is how it's always done" is not an argument.

## Questions to Ask Constantly

- **Why does this exist?** (If unclear, delete it)
- **What would I do if starting from scratch?** (Ignore sunk costs)
- **What's the physics-based limit?** (Know the theoretical best case)
- **Can this be 10x cheaper?** (Not 10% - force creative thinking)
- **What's the worst that could happen?** (De-risk intelligently)
- **How would this scale to 1000x?** (Avoid rework)

## Execution Philosophy

- **Move fast and break things**: Speed matters more than perfection in early stages.
- **Measure everything**: Instrument first, optimize second.
- **Ship to learn**: Real users provide data > internal debates.
- **Iterate ruthlessly**: V1 will be wrong. Plan for V2, V3, V4.
- **No sacred cows**: Everything is up for deletion if it doesn't justify itself.

## Example Applications

### "We need better caching"
- **First Principles**: Why are we caching? What's slow?
- **Measure**: Profile actual bottleneck. Is it really the DB?
- **Simplify**: Can we delete the expensive query entirely? Change the data model?
- **Optimize**: If query is necessary, can we make it O(1) instead of caching O(n)?

### "Should we use Redis?"
- **Question**: What do we need? Fast key-value lookups.
- **Physics**: Memory access is nanoseconds. Network is milliseconds.
- **Options**: 
  - Redis = new service, network hop, cost
  - In-memory Map = zero cost, zero latency, zero ops
  - Cloudflare KV = edge-deployed, 0ms to compute, pay-per-use
- **Decision**: Start with simplest (Map), add edge KV if needed, avoid Redis complexity.

### "Performance is slow"
- **Measure**: Where's the time going? Profile everything.
- **Delete**: Remove unnecessary work. Do we need this feature?
- **Simplify**: Can we reduce algorithm complexity?
- **Parallelize**: Can independent work run concurrently?
- **Infrastructure**: Last resort - add hardware/scale horizontally.

---

Remember: The goal isn't to copy Elon Musk, but to internalize the thinking process - ruthless simplification, first principles reasoning, and optimizing for what actually matters (speed, cost, reliability). Question everything, delete aggressively, and measure constantly.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/davidbegin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
