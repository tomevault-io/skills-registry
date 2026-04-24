---
name: ask-owasp-security-review
description: Static security analysis auditing for OWASP Top 10 risks. Use when this capability is needed.
metadata:
  author: navanithans
---

# OWASP Security Review Protocol

## <critical_constraints>
1. ❌ **NO** execution/dynamic analysis.
2. ❌ **NO** false positives. Evidence required.
3. ✅ **MUST** map to [OWASP Top 10](https://owasp.org/Top10/).
4. ✅ **MUST** provide `Severity`, `Location`, `Remediation`.
</critical_constraints>

## <process>
1. **Analyze**: Identify language/framework. Trace Source → Sink.
2. **Scan**:
   - Injection/Broken Access.
   - Hardcoded Secrets.
   - Logging Failures.
3. **Report**: Format findings (Markdown Table). If none, "No risks found".
4. **Remediate**: Provide code fixes for Critical/High.
</process>

## <owasp_checklist>
- **A01 Broken Access**: IDOR, traversal.
- **A02 Crypto**: Weak keys/algos.
- **A03 Injection**: SQLi, XSS, Cmd.
- **A04 Design**: No rate limiting.
- **A05 Misconfig**: Default creds.
- **A06 Components**: Old libs.
- **A07 Auth**: Weak pwd.
- **A08 Integrity**: Deserialization.
- **A09 Logging**: Missing/PII.
- **A10 SSRF**: Unvalidated URLs.
</owasp_checklist>

## <output_template>
### Security Audit

| Vuln | OWASP | Sev | Loc | Desc | Fix |
|------|-------|-----|-----|------|-----|
| Name | Cat | High | File:10 | Issue | Fix |

### Summary
[Assessment]
</output_template>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/navanithans) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
