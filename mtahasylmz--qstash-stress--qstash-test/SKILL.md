---
name: qstash-test
description: | Use when this capability is needed.
metadata:
  author: mtahasylmz
---

# qstash-test

Run qstash-stress YAML test suites against real QStash.

## Prerequisites

1. **Build the CLI:**
   ```bash
   go build -o qstash-stress .
   ```

2. **Set environment variables:**
   ```bash
   export QSTASH_TOKEN="your-token"
   export QSTASH_CURRENT_SIGNING_KEY="sig_..."
   export QSTASH_NEXT_SIGNING_KEY="sig_..."
   ```

3. **Start ngrok tunnel:**
   ```bash
   ngrok http 8080
   # Copy the https URL
   export RECEIVER_BASE_URL="https://xxx.ngrok.io"
   ```

## Run Tests

### Basic Usage (Recommended)

Run with `--serve` to start receiver in same process:

```bash
./qstash-stress run test-suites/basic.yaml --serve --port 8080
```

### Run Specific Suite

```bash
./qstash-stress run test-suites/retry.yaml --serve --port 8080
./qstash-stress run test-suites/queue.yaml --serve --port 8080
./qstash-stress run test-suites/callback.yaml --serve --port 8080
```

### Run Multiple Suites

```bash
./qstash-stress run test-suites/*.yaml --serve --port 8080
```

### Filter by Tags

```bash
# Only run tests tagged "basic"
./qstash-stress run test-suites/*.yaml --tags basic --serve

# Exclude tests tagged "slow"
./qstash-stress run test-suites/*.yaml --exclude-tags slow --serve
```

### Generate Reports

```bash
# Markdown report
./qstash-stress run test-suites/basic.yaml --serve --format markdown -o report.md

# JSON report
./qstash-stress run test-suites/basic.yaml --serve --format json -o report.json
```

## Available Test Suites

| Suite | Tests | Runnable | What It Tests |
|-------|-------|----------|---------------|
| basic.yaml | 9 | 9 | HTTP methods, payloads, headers |
| timing.yaml | 4 | 4 | Delays, scheduled delivery |
| dedup.yaml | 4 | 4 | ID and content-based deduplication |
| retry.yaml | 8 | 7 | Retry count, delays, Retry-After |
| callback.yaml | 5 | 2 | Success/failure callbacks |
| queue.yaml | 8 | 6 | FIFO ordering, parallelism |
| urlgroup.yaml | 4 | 1 | URL group fanout |
| batch.yaml | 4 | 2 | Batch publishing |
| dlq.yaml | 4 | 2 | Dead letter queue |
| edge-cases.yaml | 4 | 3 | Large payloads |

**Skipped suites** (require multi-step workflow):
- schedule.yaml (9 tests) - Cron scheduling
- flowcontrol.yaml (6 tests) - Rate limiting

## Troubleshooting

**Tests timeout:**
- Ensure `--serve` flag is used
- Check ngrok tunnel is active
- Verify `RECEIVER_BASE_URL` matches ngrok URL

**Signature verification failed:**
- Check signing keys match QStash console
- Both current and next keys required

**Message not delivered:**
- Check QStash console for message status
- Run with verbose logging: `./qstash-stress -v run ...`

## Load Testing

For sustained load tests:

```bash
./qstash-stress load \
  --profile constant \
  --rate 10 \
  --duration 5m \
  --destination "${RECEIVER_BASE_URL}/message"
```

Profiles: `constant`, `ramping`, `burst`, `spike`, `soak`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mtahasylmz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
