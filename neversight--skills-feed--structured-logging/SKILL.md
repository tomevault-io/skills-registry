---
name: structured-logging
description: > Use when this capability is needed.
metadata:
  author: neversight
---

# Structured Logging

RFC-34 compliant structured logging standards for Java services.

## When to use this skill

- Implementing logging in new Java services
- Converting unstructured logs to structured format
- Reviewing logging practices
- Configuring Logback for JSON output
- Adding business context to logs

## Skill Contents

### Sections

- [When to use this skill](#when-to-use-this-skill) (L24-L31)
- [Quick Start](#quick-start) (L51-L71)
- [Required Fields](#required-fields) (L72-L86)
- [Best Practices](#best-practices) (L87-L105)
- [References](#references) (L106-L111)
- [Related Rules](#related-rules) (L112-L115)
- [Related Skills](#related-skills) (L116-L121)

### Available Resources

**📚 references/** - Detailed documentation
- [logging standards](references/logging-standards.md)

---

## Quick Start

### 1. Add Dependencies

```groovy
implementation 'org.springframework.boot:spring-boot-starter-logging'
implementation 'net.logstash.logback:logstash-logback-encoder:${latest_version}'
```

### 2. Use Structured Arguments

```java
import static net.logstash.logback.argument.StructuredArguments.kv;

log.info("Transaction processed",
         kv("transaction_id", txn.getId()),
         kv("user_id", user.getId()));
```

This produces JSON with separate fields for `transaction_id` and `user_id`.

## Required Fields

All logs must include these fields:

| Field | Description |
|-------|-------------|
| `@timestamp` | Log timestamp |
| `message` | Log message text |
| `logger` | Logger name |
| `thread_name` | Thread name |
| `level` | Log level (INFO, WARN, ERROR, etc.) |
| `dd.service` | Service name |
| `dd.env` | Environment |
| `dd.version` | Service version |

## Best Practices

- Add business identifiers (IDs) as separate fields instead of embedding in messages
- Keep log message text clear and concise
- Use appropriate log levels consistently
- Include enough context to understand the event without additional queries
- Use snake_case for field names
- For logs containing objects, properly structure them rather than using `toString()`

### Example

```java
// ✅ Good - structured fields
log.info("Order created", kv("order_id", orderId), kv("user_id", userId), kv("amount", amount));

// ❌ Bad - embedded in message
log.info("Order {} created for user {} with amount {}", orderId, userId, amount);
```

## References

| Reference | Description |
|-----------|-------------|
| [references/logging-standards.md](references/logging-standards.md) | Complete RFC-34 implementation guide |

## Related Rules

- `.cursor/rules/java-structured-logs.mdc` - Full logging standards

## Related Skills

| Skill | Purpose |
|-------|---------|
| [java-standards](../java-standards/SKILL.md) | General Java standards |
| [java-testing](../java-testing/SKILL.md) | Testing log output |
<!-- AUTO-GENERATED FILE - DO NOT EDIT DIRECTLY -->
<!-- Source: bitsoex/ai-code-instructions → java/skills/structured-logging/SKILL.md -->
<!-- To modify, edit the source file and run the distribution workflow -->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
