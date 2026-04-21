---
name: analyze-error-logs
description: Analyze application logs to identify errors, patterns, and root causes Use when this capability is needed.
metadata:
  author: luanps2
---

# Analyze Error Logs Skill

## Purpose

Systematically analyze application logs to identify errors, understand patterns, and pinpoint root causes for debugging.

## Log Analysis Process

### 1. Collect Relevant Logs

```bash
# Recent application logs
tail -n 500 /var/log/app.log

# Filter by error level
grep -i "error\|exception\|fatal" /var/log/app.log

# Time-specific logs
grep "2024-02-09 14:" /var/log/app.log

# Multi-file search
find /var/log -name "*.log" -exec grep -l "OutOfMemory" {} \;
```

###2. Identify Error Patterns

Look for:
- **Repeated errors**: Same error appearing multiple times
- **Error clusters**: Errors occurring in rapid succession
- **Timing patterns**: Errors at specific times (e.g., midnight batch jobs)
- **User patterns**: Errors for specific users or actions
- **Cascading failures**: One error triggering others

### 3. Extract Key Information

From each error, extract:
- **Timestamp**: When did it occur?
- **Error type**: Exception name, error code
- **Error message**: Human-readable description
- **Stack trace**: Code path that led to error
- **Context**: User ID, request ID, session ID
- **Input data**: What data caused the issue?

### 4. Common Error Patterns

#### Database Connection Errors
```
Error: ECONNREFUSED 127.0.0.1:5432
  → Database not running or connection refused
  → Check database service status
```

#### Authentication Errors
```
Error: jwt expired
  → Token expiration issue
  → Check token TTL and refresh logic
```

#### Null/Undefined Errors
```
TypeError: Cannot read property 'id' of undefined
  → Missing data validation
  → Check data flow and null checks
```

#### Rate Limiting
```
Error: 429 Too Many Requests
  → Rate limit exceeded
  → Check rate limiter configuration
```

### 5. Correlation Analysis

```bash
# Find all errors with same correlation ID
grep "correlation-id: abc-123" /var/log/app.log

# Timeline view of errors
grep "ERROR" /var/log/app.log | awk '{print $1, $2, $NF}'
```

### 6. Root Cause Identification

Ask:
1. **What triggered this error?** (User action, cron job, external event)
2. **Why did it fail?** (Missing data, invalid state, network issue)
3. **Where in the code?** (Stack trace shows exact location)
4. **Has this happened before?** (Search historical logs)
5. **What changed recently?** (Recent deployments, config changes)

## Log Analysis Tools

- **grep/awk**: Command-line log analysis
- **jq**: JSON log parsing
- **Splunk/ELK**: Enterprise log aggregation
- **CloudWatch/Stackdriver**: Cloud-native logging
- **Sentry/Rollbar**: Error tracking platforms

## Example Analysis

**Scenario**: Users reporting login failures

```
Step 1: Search for login errors
$ grep "login" /var/log/app.log | grep -i "error"

Step 2: Identify pattern
- All errors occur for OAuth login
- Error message: "Invalid token"
- Started at 2024-02-09 14:30:00

Step 3: Correlate with deployments
$ git log --since="2024-02-09 14:00" --until="2024-02-09 15:00"
- Found: OAuth token validation logic changed in recent deploy

Step 4: Root cause
- Recent deployment changed token validation
- New validation is stricter, rejecting valid tokens
- Solution: Revert validation change or fix validation logic
```

## Best Practices

1. **Structure logs**: Use structured logging (JSON) for easier parsing
2. **Add context**: Include correlation IDs, user IDs, request IDs
3. **Log levels**: Use appropriate levels (DEBUG, INFO, WARN, ERROR, FATAL)
4. **Centralize logs**: Aggregate logs from all services
5. **Set up alerts**: Proactive notification for critical errors

## Related Skills

- `propose_fix` - Propose solutions based on analysis

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luanps2) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
