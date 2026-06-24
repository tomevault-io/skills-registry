---
name: security-review
description: Review code changes for security vulnerabilities against Meridian's security architecture Use when this capability is needed.
metadata:
  author: shreyasdbz
---

Perform a security review of: $ARGUMENTS

## Checklist

Review against Meridian's security architecture (Section 6 of `docs/architecture.md`):

### OWASP LLM Top 10
- [ ] **LLM01 Prompt Injection**: Is external content properly tagged with provenance? Are instruction/data boundaries maintained?
- [ ] **LLM02 Sensitive Info Disclosure**: Are secrets excluded from LLM context? Is PII stripped from memories?
- [ ] **LLM03 Supply Chain**: Are Gear manifests signed? Are dependencies locked?
- [ ] **LLM05 Improper Output Handling**: Are execution plans validated against schema? Are Gear parameters sanitized?
- [ ] **LLM06 Excessive Agency**: Does Sentinel validate the plan? Are high-risk actions gated on user approval?
- [ ] **LLM10 Unbounded Consumption**: Are token limits, cost limits, and timeouts enforced?

### Credential Security
- [ ] No secrets in LLM prompts or logs
- [ ] Secrets encrypted at rest (AES-256-GCM)
- [ ] Per-secret ACLs enforced

### Input Validation
- [ ] All external inputs validated at boundary
- [ ] No directory traversal possible
- [ ] No shell command injection possible
- [ ] No SQL injection possible (parameterized queries)

### Gear Sandboxing
- [ ] Gear runs in isolated environment
- [ ] Permissions match manifest declarations
- [ ] Private IP ranges blocked for network requests
- [ ] Resource limits enforced

### Authentication
- [ ] Bridge auth mandatory
- [ ] Session tokens are secure (HTTP-only, Secure, SameSite=Strict)
- [ ] Component messages signed (HMAC-SHA256)

Report findings with severity (Critical / High / Medium / Low / Info).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shreyasdbz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
