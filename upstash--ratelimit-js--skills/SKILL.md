---
name: upstash-ratelimit-ts
description: Lightweight guidance for using the Redis Rate Limit TypeScript SDK, including setup steps, basic usage, and pointers to advanced algorithm, features, pricing, and traffic‑protection docs. Use when this capability is needed.
metadata:
  author: upstash
---

# Rate Limit TS SDK

## Quick Start
- Install the SDK and connect to Redis.
- Create a rate limiter and apply it to incoming operations.

Example:
```ts
import { Ratelimit } from "@upstash/ratelimit";
import { Redis } from "@upstash/redis";

const redis = new Redis({ url: "<url>", token: "<token>" });
const limiter = new Ratelimit({ redis, limiter: Ratelimit.slidingWindow(5, "10s") });

const { success } = await limiter.limit("user-id");
if (!success) {
  // throttled
}
```

## Other Skill Files
- **algorithms.md**: Describes all available rate‑limiting algorithms and how they behave.
- **pricing-cost.md**: Explains pricing, Redis cost implications, and operational considerations.
- **features.md**: Lists SDK features such as prefixes, custom keys, and behavioral options.
- **methods-getting-started.md**: Full method reference for the SDK's API and getting started guide.
- **traffic-protection.md**: Guidance on applying rate limiting for traffic shaping, abuse prevention, and protection patterns.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/upstash) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
