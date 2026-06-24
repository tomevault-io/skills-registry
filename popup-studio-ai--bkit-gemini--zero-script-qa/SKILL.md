---
name: zero-script-qa
description: | Use when this capability is needed.
metadata:
  author: popup-studio-ai
---

# Zero Script QA

> Testing methodology using structured logging instead of test scripts

## Philosophy

Instead of writing and maintaining test scripts, Zero Script QA uses:
1. **Structured JSON logging** for all operations
2. **Real-time Docker log monitoring**
3. **Pattern-based verification**

## Key Principles

### 1. Structured Logging

```typescript
// Every operation logs structured JSON
logger.info({
  event: 'user.login',
  userId: user.id,
  method: 'email',
  success: true,
  duration: 234
});
```

### 2. Log Patterns

Define expected patterns for verification:

```json
{
  "feature": "user-login",
  "patterns": [
    {"event": "user.login", "success": true},
    {"event": "session.created"},
    {"event": "audit.login"}
  ]
}
```

### 3. Real-time Monitoring

```bash
# Monitor Docker logs in real-time
docker logs -f app-container 2>&1 | grep -E '"event":'
```

## Usage

```bash
# Start QA monitoring
/zero-script-qa start

# Monitor specific feature
/zero-script-qa monitor user-login

# Generate QA report
/zero-script-qa report
```

## Benefits

| Traditional Testing | Zero Script QA |
|---------------------|----------------|
| Write test scripts | Define log patterns |
| Maintain test code | Logs are automatic |
| Flaky tests | Consistent logs |
| Separate test env | Same as production |

## Log Categories

1. **Business Events**: User actions, transactions
2. **System Events**: Startup, shutdown, errors
3. **Performance Metrics**: Response times, throughput
4. **Security Events**: Auth, access control

## Integration

Works with:
- Docker Compose
- Kubernetes
- Any JSON logging system

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/popup-studio-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
