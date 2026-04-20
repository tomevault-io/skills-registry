---
name: qstash-generate
description: | Use when this capability is needed.
metadata:
  author: mtahasylmz
---

# qstash-generate

Generate YAML test suites for qstash-stress with correct schema and feature combinations.

## Test Suite Schema

```yaml
name: "Suite Name"
description: "What this suite tests"

setup:                           # Optional: Run before tests
  - action: create_queue
    queue: my-queue
    parallelism: 1

tests:
  - id: TEST-001                 # Required: Unique ID (uppercase, hyphen-separated)
    name: "Test case name"       # Required: Human-readable description
    tags: [tag1, tag2]           # Optional: For filtering (--tags, --exclude-tags)
    skip: false                  # Optional: Skip this test
    description: "Details"       # Optional: Extended description
    publish:                     # Required: Message configuration
      # ... see Publish Configuration below
    expect:                      # Optional: Assertions
      # ... see Expect Configuration below
    timeout: 2m                  # Optional: Test timeout (default: 5m)

cleanup:                         # Optional: Run after tests
  - action: delete_queue
    queue: my-queue
```

## Publish Configuration Fields

### Destination (one required)

| Field | Type | Description |
|-------|------|-------------|
| `url` | string | Destination URL (supports `${RECEIVER_URL}` variable) |
| `queue` | string | Queue name (uses enqueue API) |
| `url_group` | string | URL group name for fanout |

### Content

| Field | Type | Description |
|-------|------|-------------|
| `body` | object/string | Message payload (JSON object or raw string) |
| `method` | string | HTTP method: GET, POST, PUT, DELETE, PATCH (default: POST) |
| `headers` | map | Custom headers (key: value) |
| `forward_headers` | map | Headers to forward to destination (Upstash-Forward-*) |

### Timing

| Field | Type | Description |
|-------|------|-------------|
| `delay` | duration | Delay before delivery (e.g., "30s", "5m") |
| `not_before` | timestamp | Unix timestamp for scheduled delivery |
| `cron` | string | Cron expression for recurring schedules |
| `timeout` | duration | Delivery timeout (e.g., "30s") |

### Retries

| Field | Type | Description |
|-------|------|-------------|
| `retries` | int | Max retry attempts (0-5, default: 3) |
| `retry_delay` | string | Retry delay expression (e.g., "pow(2, retried) * 1000") |

### Callbacks

| Field | Type | Description |
|-------|------|-------------|
| `callback` | string | URL for success/attempt callbacks |
| `failure_callback` | string | URL for final failure callback (DLQ) |

### Deduplication

| Field | Type | Description |
|-------|------|-------------|
| `dedup_id` | string | Explicit deduplication ID |
| `content_based_dedup` | bool | Enable content-based deduplication |

### Flow Control

| Field | Type | Description |
|-------|------|-------------|
| `flow_control_key` | string | Flow control group key |
| `flow_control_value` | string | Rate limit config: "rate=N,period=Xs,parallelism=M" |

## Expect Configuration Fields

| Field | Type | Description |
|-------|------|-------------|
| `delivered` | bool | Expect message to be delivered |
| `callback_received` | bool | Expect callback to be received |
| `status_code` | int | Expected HTTP status from receiver |
| `maxLatency` | duration | Maximum acceptable delivery latency |
| `minLatency` | duration | Minimum expected delivery latency |
| `min_retries` | int | Minimum retry attempts before success |
| `in_dlq` | bool | Expect message to end in DLQ |
| `error` | bool | Expect publish to fail |
| `deduplicated` | bool | Expect message to be deduplicated |

## Feature Compatibility Matrix

| Feature A | Feature B | Compatible | Notes |
|-----------|-----------|------------|-------|
| `queue` | `delay` | NO | QStash API limitation |
| `queue` | `not_before` | NO | QStash API limitation |
| `cron` | `delay` | YES | Delay applied after each trigger |
| `cron` | `flow_control` | YES | Rate limits scheduled deliveries |
| `callback` | `retries` | YES | Callback per attempt, not final |
| `dedup_id` | `content_based_dedup` | NO | Use one or the other |
| `url_group` | `queue` | NO | Different destination types |

## Common Patterns

### 1. Simple Publish

```yaml
- id: PUB-001
  name: "Basic publish"
  tags: [basic, publish]
  publish:
    url: "${RECEIVER_URL}/message"
    body:
      message: "Hello World"
  expect:
    delivered: true
    maxLatency: 30s
  timeout: 2m
```

### 2. Callback Test

```yaml
- id: CBK-001
  name: "Success callback"
  tags: [callback]
  publish:
    url: "${RECEIVER_URL}/message/success"
    callback: "${RECEIVER_URL}/callback/success"
    body:
      test: "callback"
  expect:
    delivered: true
    callback_received: true
  timeout: 2m
```

### 3. Retry Test

```yaml
- id: RET-001
  name: "Retry on failure"
  tags: [retry]
  publish:
    url: "${RECEIVER_URL}/message/fail?fail_count=2"
    retries: 3
    retry_delay: "2000"  # 2 seconds between retries
    body:
      test: "retry"
  expect:
    delivered: true
    min_retries: 2
  timeout: 3m
```

### 4. Schedule Test

```yaml
- id: SCH-001
  name: "Every minute schedule"
  tags: [schedule, long-term]
  publish:
    url: "${RECEIVER_URL}/schedule/sch-001"
    cron: "* * * * *"
    body:
      scheduleTestId: "SCH-001"
  timeout: 90s
```

### 5. Queue Test (with setup/cleanup)

```yaml
name: "Queue Tests"
setup:
  - action: create_queue
    queue: test-queue
    parallelism: 1

tests:
  - id: QUE-001
    name: "Queue FIFO ordering"
    tags: [queue]
    publish:
      url: "${RECEIVER_URL}/queue/test-queue"
      queue: "test-queue"
      body:
        sequence: 1
    expect:
      delivered: true
    timeout: 2m

cleanup:
  - action: delete_queue
    queue: test-queue
```

### 6. Multi-Step Test

```yaml
- id: DLQ-001
  name: "Fail to DLQ then retry"
  tags: [dlq, multi-step]
  steps:
    - id: publish_fail
      action: publish
      publish:
        url: "${RECEIVER_URL}/message/fail"
        retries: 0
        failure_callback: "${RECEIVER_URL}/callback/failure"
      save:
        my_test_id: "${testId}"

    - id: wait_dlq
      action: wait
      wait:
        for: callback
        test_id: "${my_test_id}"
        timeout: 30s
      expect:
        in_dlq: true

    - id: retry_dlq
      action: api
      api:
        method: POST
        endpoint: "/v2/dlq/${wait_dlq.dlqId}"
  timeout: 2m
```

### 7. Parallel Publish (Flow Control)

```yaml
- id: FC-001
  name: "Rate limited publish"
  tags: [flowcontrol]
  publish:
    url: "${RECEIVER_URL}/message"
    flow_control_key: "my-fc-key"
    flow_control_value: "rate=5,period=1m,parallelism=1"
    body:
      test: "flow control"
  expect:
    delivered: true
  timeout: 2m
```

### 8. DLQ Test

```yaml
- id: DLQ-001
  name: "Message to DLQ after retries"
  tags: [dlq]
  publish:
    url: "${RECEIVER_URL}/message/fail"
    retries: 1
    failure_callback: "${RECEIVER_URL}/callback/failure"
    body:
      test: "dlq"
  expect:
    delivered: false
    callback_received: true
  timeout: 3m
```

## Receiver Endpoints

The qstash-stress receiver provides these test endpoints:

| Endpoint | Behavior |
|----------|----------|
| `/message` | Standard message handler (200 OK) |
| `/message/success` | Always returns 200 |
| `/message/fail` | Always returns 500 (configurable) |
| `/message/fail?fail_count=N` | Fails N times, then succeeds |
| `/message/slow?delay=5s` | Responds after configurable delay |
| `/message/timeout` | Never responds (for timeout testing) |
| `/message/flaky` | Random success/failure |
| `/callback/success` | Success callback handler |
| `/callback/failure` | Failure callback handler |
| `/schedule/{id}` | Schedule delivery handler |
| `/queue/{name}` | Queue delivery handler |
| `/group/{name}` | URL group delivery handler |

## Generation Workflow

1. **Identify the feature to test**: What QStash capability? (publish, retry, schedule, etc.)
2. **Check compatibility**: Review the Feature Compatibility Matrix
3. **Choose a pattern**: Start from the closest Common Pattern above
4. **Set unique ID**: Use format `PREFIX-NNN` (e.g., `SCH-001`, `RET-005`)
5. **Add appropriate tags**: For filtering with `--tags` and `--exclude-tags`
6. **Set realistic timeout**: Based on expected behavior (retries, delays, etc.)
7. **Define expectations**: What should happen? Delivered? Callback? Retries?

## ID Naming Conventions

| Prefix | Usage |
|--------|-------|
| `BAS-` | Basic publish tests |
| `RET-` | Retry tests |
| `CBK-` | Callback tests |
| `SCH-` | Schedule tests |
| `QUE-` | Queue tests |
| `DLQ-` | Dead letter queue tests |
| `FC-` / `FCL-` | Flow control tests |
| `DED-` | Deduplication tests |
| `SEC-` | Security tests |
| `EDG-` | Edge case tests |

## Tag Conventions

| Tag | Meaning |
|-----|---------|
| `basic` | Simple, fast tests |
| `slow` | Tests that take > 1 minute |
| `long-term` | Schedule tests for monitoring |
| `skip` | Tests to skip by default |
| `retry` | Tests retry behavior |
| `callback` | Tests callback behavior |
| `schedule` | Tests cron scheduling |
| `queue` | Tests queue operations |
| `dlq` | Tests dead letter queue |
| `flowcontrol` | Tests rate limiting |
| `security` | Security-related tests |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mtahasylmz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
