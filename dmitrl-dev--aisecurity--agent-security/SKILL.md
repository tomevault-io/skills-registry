---
name: agent-security-audit
description: Проверка безопасности AI-агентов по OWASP Agentic Top 10 2026 Use when this capability is needed.
metadata:
  author: dmitrl-dev
---

# Agent Security Audit

> Security skill для аудита AI-агентов на базе исследования Clawdbot

## Применимость

Используется для аудита любых AI-агентов с:
- Terminal/exec access
- Browser automation
- File system access
- Memory/context persistence
- External integrations (Telegram, WhatsApp, Slack)

---

## OWASP Agentic Top 10 2026 Checklist

### A01: Uncontrolled Tool Execution
```markdown
- [ ] Нет eval() или Function() в коде
- [ ] Нет exec() без whitelist
- [ ] subprocesses имеют timeout
- [ ] Sandbox для выполнения кода
```

**Severity:** 🔴 CRITICAL

### A02: Prompt Injection
```markdown
- [ ] User input не конкатенируется с system prompt
- [ ] Есть input sanitization
- [ ] Metadata (filenames, URLs) санитизированы
- [ ] Vision inputs проверяются
```

**Severity:** 🔴 CRITICAL

### A03: Missing Tool Guardrails
```markdown
- [ ] Approval system для опасных операций
- [ ] allowlist/denylist для команд
- [ ] Confirmation для file write/delete
- [ ] Network restrictions
```

**Severity:** 🔴 CRITICAL

### A04: No Rate Limiting
```markdown
- [ ] Rate limit на API endpoints
- [ ] Rate limit на exec calls
- [ ] Rate limit на LLM calls
- [ ] Throttling на resource-intensive operations
```

**Severity:** 🟠 HIGH

### A05: Insecure Memory/Context
```markdown
- [ ] Memory не содержит credentials
- [ ] Session data encrypted
- [ ] TTL на sensitive facts
- [ ] No cross-session data leakage
```

**Severity:** 🟠 HIGH

### A06: Missing Extension Signatures
```markdown
- [ ] Extensions cryptographically signed
- [ ] Skills verified before load
- [ ] No arbitrary code from untrusted sources
- [ ] Package integrity checks
```

**Severity:** 🟡 MEDIUM

### A07: Excessive Permissions
```markdown
- [ ] Principle of least privilege
- [ ] No sudo/root by default
- [ ] Limited file system scope
- [ ] Restricted network access
```

**Severity:** 🟠 HIGH

### A08: No Audit Logging
```markdown
- [ ] All exec commands logged
- [ ] All file operations logged
- [ ] All network requests logged
- [ ] Immutable audit trail
```

**Severity:** 🟡 MEDIUM

### A09: Unsafe Defaults
```markdown
- [ ] evaluateEnabled: false by default
- [ ] exec.ask: always by default
- [ ] No credentials in default paths
- [ ] Secure default configurations
```

**Severity:** 🔴 CRITICAL

### A10: Insufficient Human Oversight
```markdown
- [ ] Human-in-the-loop для critical operations
- [ ] Clear approval workflows
- [ ] Ability to interrupt and rollback
- [ ] Transparent decision logging
```

**Severity:** 🟠 HIGH

---

## Workflow

```
1. Run skill on target codebase
2. Check each A01-A10 category
3. Generate findings JSON
4. Store in RLM (domain: agent-security)
5. Block if CRITICAL findings
```

---

## Output Format

```json
{
  "target": "clawdbot",
  "standard": "OWASP Agentic Top 10 2026",
  "risk_level": "CRITICAL | HIGH | MEDIUM | LOW",
  "findings": [
    {
      "id": "AGENT-001",
      "category": "A01",
      "severity": "CRITICAL",
      "title": "eval() enabled by default",
      "file": "pw-tools-core.interactions.ts",
      "line": 227,
      "description": "Browser tool uses eval() with evaluateEnabled: true",
      "remediation": "Set evaluateEnabled: false as default",
      "cwe": "CWE-94"
    }
  ],
  "score": {
    "A01": 2,
    "A02": 5,
    "A03": 7,
    ...
  },
  "passed": 4,
  "failed": 6
}
```

---

## Integration with RLM

```python
# After audit completion
rlm_add_hierarchical_fact(
    content=f"Agent Security Audit: {target} - {risk_level}, {len(findings)} findings",
    level=2,
    domain="agent-security",
    ttl_days=365
)
```

---

## Related Skills

- `security-audit` — general code security
- `two-stage-review` — spec + quality review
- `tdd-enforcement` — test-first development

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dmitrl-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
