---
name: production-ready-standards
description: | Use when this capability is needed.
metadata:
  author: tgoddessana
---

# Production Ready Standards

This skill provides the Team Argonauts' standards for production-ready code — the criteria used to evaluate whether code is ready to ship.

## Severity Levels (P0-P4)

The Argonauts use a 5-level priority system aligned with FAANG incident classification:

### P0 - Critical (BLOCK DEPLOYMENT)

**Definition:** Issues that will cause immediate, severe impact in production.

**Criteria:**
- Security vulnerabilities (RCE, SQL injection, auth bypass)
- Data loss or corruption risk
- Complete system failure or cascade failures
- Missing critical error handling that crashes the app
- Hardcoded secrets or credentials

**Response:** Must fix before any deployment. No exceptions.

### P1 - High (FIX BEFORE MAJOR MILESTONE)

**Definition:** Issues that will cause significant problems within 3-6 months.

**Criteria:**
- Performance issues that degrade at scale
- Missing circuit breakers on critical paths
- N+1 queries on hot paths
- XSS, IDOR, privilege escalation
- Missing critical tests
- Accessibility blockers

**Response:** Fix before next major release. Track actively.

### P2 - Medium (SCHEDULE FIX)

**Definition:** Issues that create friction but are manageable short-term.

**Criteria:**
- Suboptimal patterns that work but aren't ideal
- Missing non-critical monitoring
- Minor accessibility issues
- Inefficient queries that aren't on hot paths
- Missing error states in UI

**Response:** Schedule fix within quarter. Add to backlog.

### P3 - Low (ADDRESS WHEN TOUCHING CODE)

**Definition:** Minor improvements that would be nice to have.

**Criteria:**
- Code smells that don't impact functionality
- Missing nice-to-have documentation
- Inconsistent naming conventions
- Minor test coverage gaps

**Response:** Fix when modifying related code. Don't create dedicated tickets.

### P4 - Nitpick (TAKE IT OR LEAVE IT)

**Definition:** Style preferences and suggestions.

**Criteria:**
- Formatting preferences
- Alternative approaches that aren't necessarily better
- "You could also..." suggestions

**Response:** Author's discretion. No pressure to address.

## Verdict Definitions

After review, the team provides one of four verdicts:

| Verdict | Meaning | Action |
|---------|---------|--------|
| **SHIP** | Ready for production | Deploy with confidence |
| **SHIP WITH NOTES** | Ready, minor issues | Deploy, track P2-P4 items |
| **REVISE** | Needs changes | Address P1 issues, re-review |
| **BLOCK** | Critical issues | Fix P0 issues immediately |

## Domain-Specific Checklists

### Backend Checklist
- [ ] Timeouts on all external calls
- [ ] Retry policies with exponential backoff
- [ ] Circuit breakers on critical paths
- [ ] Graceful degradation paths
- [ ] Connection pool configuration
- [ ] Idempotency for mutations
- [ ] Rate limiting considerations

### Security Checklist
- [ ] Input validation and sanitization
- [ ] Parameterized queries (no SQL injection)
- [ ] Output encoding (no XSS)
- [ ] Authentication on all protected routes
- [ ] Authorization checks (no IDOR)
- [ ] No hardcoded secrets
- [ ] Audit logging for sensitive operations

### Frontend Checklist
- [ ] Loading states for async operations
- [ ] Error states and recovery
- [ ] Keyboard navigation works
- [ ] Screen reader compatible
- [ ] No memory leaks (cleanup subscriptions)
- [ ] Reasonable bundle size impact
- [ ] Mobile responsive

### Database Checklist
- [ ] Indexes for query patterns
- [ ] No N+1 queries
- [ ] Pagination for list endpoints
- [ ] Transaction boundaries appropriate
- [ ] Migration rollback plan

### DevOps Checklist
- [ ] Zero-downtime deployment possible
- [ ] Rollback procedure documented
- [ ] Health checks configured
- [ ] Logging is structured
- [ ] Metrics exposed
- [ ] Alerts configured for failures

### Testing Checklist
- [ ] Critical paths have tests
- [ ] Error paths have tests
- [ ] Edge cases identified and tested
- [ ] Tests are deterministic (not flaky)
- [ ] Test names describe behavior

## The Argonauts Philosophy

> "We've seen enough production incidents to know: the code that 'works on my machine' is not the code that survives Black Friday traffic, a datacenter failover, or a determined attacker."

The Argonauts review with these principles:

1. **Paranoid but Pragmatic** - Assume failure, but ship anyway with mitigations
2. **Trade-offs are Explicit** - Every choice sacrifices something; document what
3. **Scale Thinking** - "Will this work at 10x?" is always relevant
4. **Defense in Depth** - Never rely on a single control
5. **Observable by Default** - If you can't measure it, you can't fix it

For detailed checklists by domain, see files in `references/` directory.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tgoddessana) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
