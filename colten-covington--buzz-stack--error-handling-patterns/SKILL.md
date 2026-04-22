---
name: error-handling-patterns
description: End-to-end error handling patterns for Buzz Stack (Next.js + React 19): error boundaries, graceful degradation, Result types, logging, and resilient networking. Use when this capability is needed.
metadata:
  author: colten-covington
---

# Error Handling Patterns

## Overview

In Buzz Stack, error handling is part of UX, correctness, and operational maturity.

You want a system that:

- communicates failures clearly to users
- preserves debug context for engineers
- avoids cascading failures (retry storms, request amplification)
- keeps the UI usable (graceful degradation)

This skill covers patterns spanning:

- Next.js App Router boundaries (`app/error.tsx`, `app/not-found.tsx`, `app/loading.tsx`)
- typed error contracts (`Result<T, E>`)
- network resilience (timeouts, retries, backoff, circuit breakers)
- logging and error tracking integration

## Core Concepts

### 1) Error Boundaries in Next.js App Router

Next.js App Router uses convention-based boundary files:

- `app/error.tsx` for runtime render errors (client component)
- `app/not-found.tsx` for 404-like states
- `app/loading.tsx` for suspense/loading fallback

Real Buzz Stack example: `app/error.tsx` uses a `reset()` callback and logs errors.

```tsx
"use client";

import { useEffect } from "react";

export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  useEffect(() => {
    console.error("Application error:", error);
  }, [error]);

  return (
    <div>
      <h2>Something went wrong!</h2>
      <button onClick={reset}>Try again</button>
    </div>
  );
}
```

Key boundary behaviors:

- Boundaries catch render errors, not every async error
- `reset()` should attempt to re-render and re-run data fetching
- Keep fallback UIs accessible and keyboard friendly

---

### 2) Graceful Degradation

Graceful degradation means your app stays useful when a feature fails.

Examples:

- If voice search fails, allow text search
- If live updates fail, fall back to manual refresh
- If recommendations fail, still show the core content

Feature detection pattern:

```typescript
export function supportsWebSpeech(): boolean {
  return typeof window !== "undefined" && "SpeechRecognition" in window;
}
```

---

### 3) User Feedback Patterns

A failure is an interaction. Users need:

- a clear message (what happened)
- a safe next step (retry, back, contact)
- status context (loading, retrying, offline)

Inline error pattern:

```tsx
export function InlineError({ message }: { message: string }) {
  return (
    <p role="alert" className="text-sm">
      {message}
    </p>
  );
}
```

---

### 4) Logging Strategies: Structured Context + Breadcrumbs

Logging should answer:

- what failed?
- for whom (non-PII identifiers)?
- where (route/component/action)?
- with what inputs (sanitized)?

```typescript
interface LogContext {
  scope: string;
  requestId?: string;
  userId?: string;
  tags?: Record<string, string>;
}

export function logError(error: unknown, context: LogContext): void {
  const message = error instanceof Error ? error.message : String(error);
  console.error({ level: "error", message, context });
}
```

Breadcrumb pattern:

```typescript
type Breadcrumb = {
  at: number;
  message: string;
  data?: Record<string, unknown>;
};

export class Breadcrumbs {
  private items: Breadcrumb[] = [];

  add(message: string, data?: Record<string, unknown>) {
    this.items.push({ at: Date.now(), message, data });
  }

  snapshot(): Breadcrumb[] {
    return [...this.items];
  }
}
```

---

### 5) Result Types for Predictable Failures

Use `Result<T, E>` to force handling and avoid ambiguous `undefined`.

```typescript
type Result<T, E = Error> = { ok: true; value: T } | { ok: false; error: E };

function ok<T>(value: T): Result<T, never> {
  return { ok: true, value };
}

function err<E>(error: E): Result<never, E> {
  return { ok: false, error };
}
```

---

### 6) Network Error Handling: Timeouts, Retries, Backoff, Circuit Breakers

Network failures are normal in production.

A minimal resilience toolkit:

- timeouts via `AbortController`
- bounded retries with exponential backoff
- circuit breaker for repeated failures

---

## Patterns (10+)

### Pattern 1: App Router Error Boundary With Reset

Use `reset()` to recover without forcing a full navigation.

```tsx
"use client";

export default function Error({
  error,
  reset,
}: {
  error: Error;
  reset: () => void;
}) {
  return (
    <main>
      <h1>Something went wrong</h1>
      <p role="alert">{error.message}</p>
      <button onClick={reset}>Try again</button>
    </main>
  );
}
```

Implementation tip:

- Keep the boundary UI lightweight to avoid errors-in-the-error UI.

---

### Pattern 2: Domain Errors as Discriminated Unions

```typescript
type FetchUserError =
  | { code: "NOT_FOUND" }
  | { code: "UNAUTHORIZED" }
  | { code: "UNKNOWN"; message: string };

type ResultUser = Result<{ id: string; name: string }, FetchUserError>;
```

---

### Pattern 3: “Railway” Composition for Result Types

```typescript
function mapResult<T, E, U>(res: Result<T, E>, fn: (t: T) => U): Result<U, E> {
  return res.ok ? { ok: true, value: fn(res.value) } : res;
}

function andThen<T, E, U>(
  res: Result<T, E>,
  fn: (t: T) => Result<U, E>,
): Result<U, E> {
  return res.ok ? fn(res.value) : res;
}
```

---

### Pattern 4: Timeout Wrapper for Fetch

```typescript
export async function fetchWithTimeout(
  input: RequestInfo | URL,
  init: RequestInit & { timeoutMs: number },
): Promise<Response> {
  const controller = new AbortController();
  const timeout = setTimeout(() => controller.abort(), init.timeoutMs);

  try {
    const response = await fetch(input, { ...init, signal: controller.signal });
    return response;
  } finally {
    clearTimeout(timeout);
  }
}
```

---

### Pattern 5: Exponential Backoff Retry (Bounded)

```typescript
function sleep(ms: number): Promise<void> {
  return new Promise((resolve) => setTimeout(resolve, ms));
}

export async function retry<T>(
  fn: () => Promise<T>,
  options: { retries: number; baseDelayMs: number },
): Promise<T> {
  let lastError: unknown = null;

  for (let attempt = 0; attempt <= options.retries; attempt++) {
    try {
      return await fn();
    } catch (error) {
      lastError = error;
      const delay = options.baseDelayMs * Math.pow(2, attempt);
      await sleep(delay);
    }
  }

  throw lastError instanceof Error ? lastError : new Error(String(lastError));
}
```

---

### Pattern 6: Circuit Breaker (Simple)

```typescript
type CircuitState = "CLOSED" | "OPEN" | "HALF_OPEN";

export class CircuitBreaker {
  private state: CircuitState = "CLOSED";
  private failures = 0;
  private openedAt = 0;

  constructor(
    private readonly options: {
      failureThreshold: number;
      resetAfterMs: number;
    },
  ) {}

  async exec<T>(fn: () => Promise<T>): Promise<T> {
    if (this.state === "OPEN") {
      if (Date.now() - this.openedAt > this.options.resetAfterMs) {
        this.state = "HALF_OPEN";
      } else {
        throw new Error("Circuit open");
      }
    }

    try {
      const value = await fn();
      this.failures = 0;
      this.state = "CLOSED";
      return value;
    } catch (error) {
      this.failures += 1;
      if (this.failures >= this.options.failureThreshold) {
        this.state = "OPEN";
        this.openedAt = Date.now();
      }
      throw error;
    }
  }
}
```

---

### Pattern 7: Typed Fetch Wrapper Returning `Result`

```typescript
export async function safeJson<T>(
  response: Response,
): Promise<Result<T, "INVALID_JSON">> {
  try {
    const data = (await response.json()) as T;
    return { ok: true, value: data };
  } catch {
    return { ok: false, error: "INVALID_JSON" };
  }
}

export async function fetchJsonResult<T>(
  url: string,
): Promise<Result<T, { code: "HTTP"; status: number } | { code: "NETWORK" }>> {
  try {
    const res = await fetch(url);
    if (!res.ok)
      return { ok: false, error: { code: "HTTP", status: res.status } };

    const json = await safeJson<T>(res);
    if (!json.ok) return { ok: false, error: { code: "NETWORK" } };

    return { ok: true, value: json.value };
  } catch {
    return { ok: false, error: { code: "NETWORK" } };
  }
}
```

---

### Pattern 8: Graceful Degradation With Feature Flags

```typescript
interface FeatureFlags {
  voiceSearch: boolean;
}

export function canUseVoice(flags: FeatureFlags): boolean {
  return flags.voiceSearch;
}
```

---

### Pattern 9: Inline Retry UI

```tsx
export function RetryBlock({
  message,
  onRetry,
}: {
  message: string;
  onRetry: () => void;
}) {
  return (
    <section>
      <p role="alert">{message}</p>
      <button onClick={onRetry}>Retry</button>
    </section>
  );
}
```

---

### Pattern 10: Error-to-UI Mapping Layer

Keep business errors away from direct user messaging.

```typescript
type UiError = { title: string; description: string };

type DomainError =
  | { code: "UNAUTHORIZED" }
  | { code: "NOT_FOUND" }
  | { code: "UNKNOWN"; message: string };

function toUiError(error: DomainError): UiError {
  switch (error.code) {
    case "UNAUTHORIZED":
      return {
        title: "Sign in required",
        description: "Please sign in and try again.",
      };
    case "NOT_FOUND":
      return {
        title: "Not found",
        description: "The requested item does not exist.",
      };
    case "UNKNOWN":
      return { title: "Something went wrong", description: error.message };
  }
}
```

---

### Pattern 11: Server Action Errors as Typed Results

Buzz Stack docs show a discriminated union `Result` for server actions.

```typescript
export type ActionResult<T, E = string> =
  | { success: true; data: T }
  | { success: false; error: E };
```

---

### Pattern 12: “Not Found” as a First-Class UX

Buzz Stack’s `app/not-found.tsx` demonstrates dedicated 404 UX. Prefer that over generic errors.

```tsx
import Link from "next/link";

export default function NotFound() {
  return (
    <main>
      <h1>404</h1>
      <p>The page does not exist.</p>
      <Link href="/">Go home</Link>
    </main>
  );
}
```

---

## Anti-Patterns (5+)

### Anti-pattern 1: Swallowing Errors

```typescript
try {
  await doThing();
} catch {
  // ❌ silence
}
```

Fix: log with context and return a typed failure.

---

### Anti-pattern 2: Infinite Retries

Unbounded retry loops create request storms.

Fix:

- bound retries
- exponential backoff
- circuit breaker for repeated failures

---

### Anti-pattern 3: Logging Secrets or PII

Avoid logging emails, tokens, passwords, raw request bodies.

Fix: sanitize and log stable identifiers.

---

### Anti-pattern 4: Using `alert()` for Error UX

Fix: inline errors, retry controls, and accessible status messaging.

---

### Anti-pattern 5: Throwing Strings

```typescript
// ❌
throw "bad";
```

Fix:

```typescript
throw new Error("bad");
```

---

## Real-World Buzz Stack Examples

- Error boundary: `app/error.tsx` uses `reset()` and logs errors.
- Not-found UX: `app/not-found.tsx` provides a dedicated 404.
- Typed Result patterns appear in Buzz Stack docs for server actions.

## Cross-References

- Skills:
  - `typescript-deep-dives`: ../typescript-deep-dives/SKILL.md
  - `typescript-mastery`: ../typescript-mastery/SKILL.md
  - `qa-testing`: ../qa-testing/SKILL.md
  - `security-vulnerability`: ../security-vulnerability/SKILL.md

- Docs:
  - Troubleshooting: ../../../docs/TROUBLESHOOTING.md
  - Monitoring/debugging: ../../../docs/MONITORING-DEBUGGING.md
  - Next.js best practices (Result patterns): ../../../docs/NEXTJS-BEST-PRACTICES.md

## Quick Checklist

- Is there a boundary UI for unexpected render failures?
- Are expected failures modeled as `Result`?
- Are network retries bounded with backoff?
- Is logging structured and sanitized?
- Do users always have a safe next action?

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/colten-covington) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
