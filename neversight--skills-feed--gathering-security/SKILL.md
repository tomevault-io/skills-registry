---
name: gathering-security
description: Use when working with the drum sounds. Spider and Raccoon gather for complete security work. Use when implementing auth or auditing security end-to-end.
metadata:
  author: neversight
---

# Gathering Security 🌲🕷️🦝

The drum echoes in the shadows. The Spider weaves intricate webs of authentication, each strand placed with precision. The Raccoon rummages through every corner, finding what doesn't belong, cleaning what could harm. Together they secure the forest—doors locked tight, secrets safe, paths protected.

## When to Summon

- Implementing authentication systems
- Adding OAuth or session management
- Security auditing before launch
- After security incidents
- Preparing for production deployment
- When auth and security audit must work together

---

## The Gathering

```
SUMMON → ORGANIZE → EXECUTE → VALIDATE → COMPLETE
   ↓         ↲          ↲          ↲          ↓
Receive  Dispatch   Animals    Verify   Security
Request  Animals    Work       Check    Hardened
```

### Animals Mobilized

1. **🕷️ Spider** — Weave authentication webs with patient precision
2. **🦝 Raccoon** — Rummage for security risks and cleanup

---

### Phase 1: SUMMON

*The drum sounds. The shadows shift...*

Receive and parse the request:

**Clarify the Security Work:**
- Adding new auth provider? (OAuth, SSO)
- Securing routes and APIs?
- General security audit?
- Post-incident cleanup?

**Scope Check:**
> "I'll mobilize a security gathering for: **[security work]**
> 
> This will involve:
> - 🕷️ Spider weaving authentication
>   - OAuth/PKCE flow
>   - Session management
>   - Route protection
>   - Token handling
> - 🦝 Raccoon auditing security
>   - Secret scanning
>   - Vulnerability check
>   - Input validation review
>   - Access control verification
> 
> Proceed with the gathering?"

---

### Phase 2: ORGANIZE

*The animals take their positions in the shadows...*

Dispatch in sequence:

**Dispatch Order:**

```
Spider ──→ Raccoon
   │          │
   │          │
Weave      Audit
Auth       Security
```

**Dependencies:**
- Spider must complete before Raccoon (needs auth to audit)
- May iterate: Raccoon findings → Spider fixes → Raccoon re-audit

**Iteration Cycle (When Vulnerabilities Found):**

```
┌─────────────────────────────────────────────────────────┐
│                   SECURITY ITERATION                     │
├─────────────────────────────────────────────────────────┤
│                                                          │
│   🕷️ Spider weaves auth    ─────►    🦝 Raccoon audits  │
│         ▲                                   │            │
│         │                                   ▼            │
│         │                          Vulnerabilities?      │
│         │                             /        \         │
│         │                          Yes          No       │
│         │                           │            │       │
│         └───── Spider fixes ◄───────┘            │       │
│                                                  ▼       │
│                                            ✅ Secure     │
└─────────────────────────────────────────────────────────┘
```

**Iteration Rules:**
- Raccoon finds vulnerability → Spider patches → Raccoon re-audits that specific fix
- Maximum 3 iterations per issue (if more needed, architectural review required)
- Each iteration focuses only on newly found/fixed items
- Document all iterations in final report

---

### Phase 3: EXECUTE

*The web is woven. The audit begins...*

Execute each phase:

**🕷️ SPIDER — WEAVE**

```
"Spinning the authentication threads..."

Phase: SPIN
- Choose auth pattern (OAuth 2.0 + PKCE, JWT, Session)
- Set up infrastructure (client registration, secrets)

Phase: CONNECT
- Implement OAuth flow (login/callback)
- Session/token management
- User info fetching

Phase: SECURE
- Route protection middleware
- CSRF protection
- Rate limiting
- Security headers

Phase: TEST
- Auth flow end-to-end
- Error handling
- Edge cases

Phase: BIND
- Documentation
- Environment variables
- Monitoring

Output:
- Working authentication system
- Protected routes
- Session management
```

**🦝 RACCOON — AUDIT**

```
"Rummaging through every corner..."

Phase: RUMMAGE
- Search for secrets in code
- Check git history
- Scan dependencies for vulnerabilities

Phase: INSPECT
- Validate auth implementation
- Check input validation
- Review access controls
- Examine error messages

Phase: SANITIZE
- Remove any secrets found
- Rotate exposed credentials
- Patch vulnerabilities

Phase: PURGE
- Clean git history if needed
- Remove dead code
- Clear old tokens

Phase: VERIFY
- Re-scan for secrets
- Verify fixes
- Install pre-commit hooks

Output:
- Security audit report
- Issues fixed
- Preventive measures in place
```

---

### Phase 4: VALIDATE

*The web holds. The audit confirms...*

**Validation Checklist:**

- [ ] Spider: Auth flow works end-to-end
- [ ] Spider: Routes properly protected
- [ ] Spider: Sessions expire correctly
- [ ] Spider: CSRF protection active
- [ ] Raccoon: No secrets in codebase
- [ ] Raccoon: Dependencies up to date
- [ ] Raccoon: Input validation present
- [ ] Raccoon: No sensitive data in logs
- [ ] Raccoon: Pre-commit hooks installed

**Security Test Cases:**

```
Authentication:
□ Login redirects to provider
□ Callback exchanges code for tokens
□ Sessions created correctly
□ Logout clears sessions
□ Expired tokens rejected

Authorization:
□ Protected routes require auth
□ Admin routes check roles
□ API endpoints verify tokens
□ Users can't access others' data

Input Validation:
□ SQL injection prevented
□ XSS prevented
□ File uploads sanitized
□ Rate limiting active
```

---

### Phase 5: COMPLETE

*The gathering ends. The forest is secure...*

**Completion Report:**

```markdown
## 🌲 GATHERING SECURITY COMPLETE

### Security Work: [Description]

### Animals Mobilized
🕷️ Spider → 🦝 Raccoon

### Authentication Implemented
- **Provider:** [OAuth 2.0 / GitHub / Google / etc.]
- **Flow:** [PKCE / Authorization Code]
- **Session Type:** [Token / Session Cookie]
- **Routes Protected:** [count]

### Security Measures
- CSRF protection: ✅
- Rate limiting: ✅ [limits]
- Security headers: ✅
- Input validation: ✅
- Secret scanning: ✅ Clean

### Vulnerabilities Addressed
- [List any found and fixed]

### Preventive Measures
- Pre-commit hooks installed
- Dependency scanning enabled
- Security headers configured
- Monitoring alerts set

### Files Created/Modified
- Auth routes: [files]
- Middleware: [files]
- Configuration: [files]

### Time Elapsed
[Duration]

*The forest sleeps securely.* 🌲
```

---

## Example Gathering

**User:** "/gathering-security Add GitHub OAuth and security audit"

**Gathering execution:**

1. 🌲 **SUMMON** — "Mobilizing for: GitHub OAuth + security audit. New auth provider needed."

2. 🌲 **ORGANIZE** — "Spider implements → Raccoon audits"

3. 🌲 **EXECUTE** —
   - 🕷️ Spider: "OAuth client registered, PKCE flow implemented, sessions working, routes protected"
   - 🦝 Raccoon: "No secrets found, dependencies clean, input validated, rate limiting added"

4. 🌲 **VALIDATE** — "Auth works, audit clean, all security checks pass"

5. 🌲 **COMPLETE** — "GitHub OAuth live, security hardened"

---

*Woven tight and audited clean—the forest is safe.* 🌲

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
