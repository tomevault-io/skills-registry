---
name: qstash-analyze
description: | Use when this capability is needed.
metadata:
  author: mtahasylmz
---

# qstash-analyze

Analyze qstash-stress codebase structure, trace execution flow, and explain design decisions.

## Quick Reference

Read `CLAUDE.md` in the project root for comprehensive architecture documentation.

## Analysis Workflows

### Trace Message Flow

1. **Publish path**: `cmd/publish.go` → `publisher/client.go:Publish()` → QStash API
2. **Receive path**: QStash → `receiver/handlers.go:handleMessage()` → `verify/signature.go` → `tracker/tracker.go:RecordDelivery()`
3. **Test path**: `runner/runner.go:RunTest()` → publish → `tracker.WaitForDelivery()` → validate expectations

### Find Feature Implementation

| QStash Feature | Primary Location |
|----------------|------------------|
| Publish | `internal/publisher/client.go:112-272` |
| Batch | `internal/publisher/client.go:289-329` |
| Queues | `internal/publisher/queue.go` |
| Schedules | `internal/publisher/schedule.go` |
| URL Groups | `internal/publisher/urlgroup.go` |
| DLQ | `internal/publisher/dlq.go` |
| Callbacks | `publisher/client.go:180-210` (headers), `receiver/handlers.go` (receive) |
| Retries | `publisher/client.go:165-170` (Upstash-Retries, Upstash-Retry-Delay) |
| Delays | `publisher/client.go:155-160` (Upstash-Delay, Upstash-Not-Before) |
| Deduplication | `publisher/client.go:220-225` |
| Flow Control | `publisher/client.go:230-235` |
| Signature Verify | `internal/verify/signature.go:14-64` |

### Explain Design Decisions

| Decision | Rationale | Location |
|----------|-----------|----------|
| `--serve` mode | Share tracker between runner and receiver in same process | `cmd/run.go:82-105` |
| `sync.Map` | Lock-free reads for high-concurrency tracking | `tracker/tracker.go:40-52` |
| Per-message `RWMutex` | Fine-grained locking without global contention | `tracker/tracker.go:23-37` |
| Reservoir sampling | Memory-bounded metrics (10K samples max) | `tracker/metrics.go:65-107` |
| Dual signing keys | Support QStash key rotation | `verify/signature.go:14-19` |
| YAML test suites | Declarative, version-controlled test definitions | `runner/config.go` |

### Debug Common Issues

**Message not delivered:**
1. Check `RECEIVER_BASE_URL` matches ngrok URL
2. Verify signing keys match QStash console
3. Check `/metrics` endpoint for tracker state
4. Run with `-v` for debug logs

**Test timeout:**
1. Confirm `--serve` flag is used (shared tracker)
2. Check QStash console for message status
3. Verify receiver is accessible from internet

**Signature verification failed:**
1. Check `QSTASH_CURRENT_SIGNING_KEY` and `QSTASH_NEXT_SIGNING_KEY`
2. Keys are in QStash console under "Signing Keys"
3. Both current and next are required

## Code Navigation Patterns

To find implementation of a specific header:
```bash
grep -r "Upstash-HeaderName" internal/publisher/
```

To find where a type is used:
```bash
grep -r "TypeName" --include="*.go"
```

To trace a function call chain:
1. Start at entry point (cmd/*.go)
2. Follow function calls to internal packages
3. Note the tracker/metrics recording points

## QStash Documentation Reference

Official docs at `../qstash/` for:
- `features/*.mdx` - Feature details
- `api/` - API reference
- `sdks/` - SDK patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mtahasylmz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
