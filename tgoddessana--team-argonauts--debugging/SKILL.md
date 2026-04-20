---
name: debugging-best-practices
description: | Use when this capability is needed.
metadata:
  author: tgoddessana
---

# Debugging Best Practices

Systematic approaches to debugging from the Team Argonauts' FAANG experience.

## The Debugging Mindset

> "A debugger is not a debugging strategy. Understanding the system is."

### Core Principles

1. **Reproduce First** - Can't fix what you can't see
2. **Hypothesize, Don't Guess** - Form theories based on evidence
3. **Change One Thing** - Multiple changes obscure the fix
4. **Verify the Fix** - Ensure you fixed the root cause, not a symptom
5. **Document** - Future you will forget why this broke

## Systematic Debugging Process

### 1. Gather Evidence

**What to collect:**
- Exact error messages (full stack traces)
- Reproduction steps
- When it started happening
- Environment details (OS, browser, versions)
- Logs around the time of failure
- User reports (what they were trying to do)

**Questions to ask:**
- Did this ever work?
- What changed recently? (code, config, dependencies, traffic)
- Can you reproduce it?
- Does it happen every time or intermittently?

### 2. Form Hypotheses

**Common culprits by symptom:**

| Symptom | Likely Causes |
|---------|---------------|
| Works locally, fails in prod | Environment differences, secrets, scale, async timing |
| Intermittent failures | Race conditions, timeouts, external dependencies |
| Slow performance | N+1 queries, missing indexes, network latency, memory |
| Memory leaks | Unclosed connections, event listener leaks, circular refs |
| Crashes | Null pointer, out of bounds, stack overflow, OOM |

### 3. Test Hypotheses

**Isolation techniques:**
- Binary search (disable half the code, which half fails?)
- Minimal reproduction (simplest code that triggers bug)
- Change one variable at a time
- Add logging/tracing at boundaries
- Use debugger breakpoints strategically

**Don't just:**
- Add random console.logs everywhere
- Try things until something works
- Guess at the problem

### 4. Verify and Document

**Before closing:**
- [ ] Root cause identified (not just symptoms)
- [ ] Fix verified in environment where it failed
- [ ] Regression test added
- [ ] Monitoring/alerting added if needed
- [ ] Postmortem written for production issues

## Debugging by Domain

### Backend Debugging

**Timeouts:**
```
Symptoms: Requests hanging, 504 errors
Check: Connection pool exhaustion, slow queries, downstream timeouts
Tools: Query explain plans, connection metrics, distributed traces
```

**Data corruption:**
```
Symptoms: Wrong data in DB, inconsistent state
Check: Transaction boundaries, race conditions, concurrent updates
Tools: DB logs, transaction isolation levels, locks
```

**Memory leaks:**
```
Symptoms: Gradual performance degradation, OOM crashes
Check: Connection leaks, cache unbounded growth, event listeners
Tools: Heap dumps, memory profilers, connection pool metrics
```

### Frontend Debugging

**Render issues:**
```
Symptoms: UI not updating, wrong data displayed
Check: State mutation, stale closures, missing dependencies
Tools: React DevTools, Redux DevTools, component profiler
```

**Performance:**
```
Symptoms: Janky UI, slow interactions, high CPU
Check: Unnecessary re-renders, large lists, layout thrashing
Tools: Performance tab, React Profiler, Lighthouse
```

**Memory leaks:**
```
Symptoms: Page slower over time, tabs crash
Check: Event listeners not removed, subscriptions not cleaned up
Tools: Memory profiler, heap snapshots, retention analysis
```

### Security Debugging

**Auth failures:**
```
Symptoms: Logged out unexpectedly, can't access resources
Check: Token expiry, CORS, cookie settings, session storage
Tools: Browser DevTools, network tab, storage inspector
```

**Data leaks:**
```
Symptoms: Users seeing wrong data
Check: Missing authz checks, cache key collisions, IDOR
Tools: Request inspection, auth middleware logs
```

## Debugging Production Issues

### Incident Response Flow

1. **Mitigate** - Stop the bleeding (rollback, feature flag, scale up)
2. **Triage** - How many users? What severity?
3. **Diagnose** - Logs, metrics, traces
4. **Fix** - Deploy fix or workaround
5. **Verify** - Confirm resolution
6. **Postmortem** - Learn and prevent recurrence

### Essential Observability

**Logs:**
- Structured (JSON) with correlation IDs
- Appropriate log levels
- No PII or secrets
- Searchable and aggregated

**Metrics:**
- RED: Rate, Errors, Duration
- Resource: CPU, memory, connections
- Business: User actions, conversions

**Traces:**
- Distributed tracing for multi-service
- Critical path instrumentation
- Span context propagation

## Common Anti-Patterns

### ❌ Print Debugging Gone Wrong
```javascript
// Don't
console.log("here 1")
console.log("here 2")
console.log("data", data) // data is 400KB object
```

### ✅ Strategic Logging
```javascript
// Do
logger.info("Processing order", {
  orderId: order.id,
  userId: user.id,
  stage: "payment"
})
```

### ❌ Changing Multiple Things
```javascript
// Don't: Changed timeout AND retry logic AND added cache
// Which one fixed it? Who knows!
```

### ✅ Isolate Variables
```javascript
// Do: Test timeout change alone first
// Then test retry logic alone
// Then test cache alone
```

### ❌ Assuming Without Evidence
```
"It's probably the database"
"Must be a network issue"
"The user did something wrong"
```

### ✅ Verify Assumptions
```
Check DB query times → Normal
Check network latency → Normal
Check user input → Found invalid data!
```

## Tools by Problem Type

### Performance Issues
- **Backend:** APM tools (DataDog, New Relic), query profilers
- **Frontend:** Lighthouse, Chrome DevTools Performance tab
- **Database:** EXPLAIN ANALYZE, slow query log

### Memory Issues
- **Backend:** Heap dumps, memory profilers
- **Frontend:** Chrome Memory profiler, heap snapshots

### Network Issues
- **Browser:** Network tab, HAR files
- **Server:** tcpdump, Wireshark, request logs

### Concurrency Issues
- **Backend:** Thread dumps, distributed tracing
- **Frontend:** React concurrent mode profiler

## The "Can't Reproduce" Problem

### Strategies:

1. **Environment Parity** - Make local match production
2. **Synthetic Data** - Use production data shapes (not values)
3. **Traffic Replay** - Replay production requests locally
4. **Feature Flags** - Enable debugging in production safely
5. **Observability** - If can't repro, observe it happening

### Data-Dependent Bugs:
- Get sanitized production data
- Check edge cases (null, empty, max values)
- Look for specific user IDs in logs

### Timing-Dependent Bugs:
- Add artificial delays
- Increase load/concurrency
- Use race condition detectors

## Postmortem Template

```markdown
# Incident Postmortem: [Title]

**Date:** [Date]
**Duration:** [Start - End]
**Impact:** [Users affected, revenue lost]
**Severity:** [P0-P4]

## Summary
[What happened in 2-3 sentences]

## Timeline
- HH:MM - Issue detected
- HH:MM - Team alerted
- HH:MM - Root cause identified
- HH:MM - Fix deployed
- HH:MM - Verified resolved

## Root Cause
[Technical explanation of what broke and why]

## Resolution
[What fixed it]

## Action Items
- [ ] Fix 1 (Owner, Due date)
- [ ] Fix 2 (Owner, Due date)

## What Went Well
[Positives to reinforce]

## What Could Be Better
[Improvements without blame]
```

## When to Escalate

Escalate when:
- User impact is severe (data loss, security, outage)
- Issue has been open >2 hours without progress
- Need domain expertise you don't have
- Workaround exists but root cause unclear

## References

For domain-specific debugging techniques, see `references/` directory.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tgoddessana) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
