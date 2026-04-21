---
name: review
description: > Use when this capability is needed.
metadata:
  author: t2hnd
---

# Review Skill

Comprehensive code review. Replicates the successful "Rigorous Pre-Release Quality Review" pattern.

## Workflow

Execute this review **autonomously** without asking questions:

### Phase 1: Security Audit

**1. Framework Security:**
- Electron: `nodeIntegration: false`, `contextIsolation: true`, `sandbox: true`
- Web: CORS config, CSP headers, HTTPS
- API: Authentication, rate limiting

**2. Injection Vulnerabilities:**
- SQL injection (parameterized queries?)
- Command injection (shell exec with user input?)
- XSS (`dangerouslySetInnerHTML`, `innerHTML`)

**3. Secrets:**
- Hardcoded API keys or passwords
- `.env` in `.gitignore`?
- Secrets in console logs

**4. Dependencies:**
- `npm audit` for known vulnerabilities
- Outdated packages with CVEs

### Phase 2: Correctness & Edge Cases

**5. Error Handling:**
- `JSON.parse()` without try-catch
- Fetch/API calls without `.catch()`
- Backend endpoints without error handling
- Missing React Error Boundaries

**6. Index & Boundary:**
- Array access without bounds checking
- Off-by-one errors in loops

**7. Null/Undefined:**
- Chained property access without `?.`
- Missing null checks before `.map()`, `.filter()`

**8. Type Safety:**
- Run `tsc --noEmit --strict`
- Flag `any` types in critical paths
- Flag unsafe type assertions

### Phase 3: Performance

**9. Memory Leaks:**
- Event listeners without cleanup
- `useEffect` without cleanup return
- Timers without `clearTimeout`/`clearInterval`

**10. Bundle Size:**
- Large unoptimized assets
- Code splitting opportunities

### Phase 4: Code Quality

**11. Documentation:**
- LICENSE file exists?
- README complete?
- Setup instructions work?

**12. Dead Code:**
- Unused imports and exports
- Commented-out code blocks

**13. Test Coverage:**
- Run coverage report
- Critical paths tested?

### Phase 5: Report

Categorize all issues:

```markdown
## P0 (MUST FIX before release)
- [ ] Security: contextIsolation disabled
- [ ] Hardcoded API key in src/config.ts:12

## P1 (SHOULD FIX)
- [ ] Missing error boundary in App.tsx
- [ ] Unhandled promise rejection in api.ts:45

## P2 (NICE TO FIX)
- [ ] Test coverage 30%
- [ ] 5 unused imports
```

After reporting, offer to auto-fix P0 issues.

### Phase 6: Verification

After fixes:
```bash
npm run build
npm run typecheck
npm test
npm audit
```

## Important Rules

- **DO prioritize issues** (P0 > P1 > P2)
- **DO provide file paths and line numbers** for every issue
- **DO offer to auto-fix P0 issues** after reporting
- **NEVER fix issues without reporting first** — user should see the audit
- **NEVER skip security checks** — critical for public release

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/t2hnd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
