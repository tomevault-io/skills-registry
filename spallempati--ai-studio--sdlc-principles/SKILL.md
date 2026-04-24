---
name: sdlc-principles
description: | Use when this capability is needed.
metadata:
  author: spallempati
---

## Universal Principles (apply to _all_ code)

1. **Single Source of Truth** – Code, tests, configs, and docs **MUST** live in
   Git; artefacts regenerate from source.
2. **Shift-Left Quality** – Lint, SAST, DAST, and data-quality checks **MUST**
   run on every PR via GitHub Actions.
3. **Compliance as Code** – Rules **MUST** map to at least one control (SOC 2,
   ISO 27001, GDPR) so audits read from Git.
4. **Performance & Reliability** – Each service **SHOULD** define SLOs (p95
   latency, availability) and include alert thresholds in code.
5. **Design for Testability** – Public contracts **SHOULD** be explicit,
   deterministic, and mock-friendly to simplify automated tests.
6. **Observability by Default** – Code **MUST** emit OpenTelemetry traces and
   New Relic-compatible metrics.
7. **Observability Hooks** – All services **SHOULD** expose structured logs and
   trace spans that unit / integration tests can assert on.

---

## New Code

8. **Cloud-Native First** – New components **MUST** be containerised, stateless,
   and deployed via GitOps workflow.
9. **Feature Flags over Forks** – Branches > 14 days are disallowed; use toggles
   to protect incomplete code.
10. **Secure-by-Default Templates** – New repos **MUST** scaffold from approved
    templates with OWASP mitigations wired in.

---

## Existing Code

11. **Upgrade-on-Touch** – When modifying legacy code, teams **SHOULD** migrate
    to supported LTS runtimes & add missing tests.
12. **Strangler Pattern** – Incrementally wrap/replace legacy modules rather
    than alter monolith internals.
13. **Runtime Compensating Controls** – Where shift-left tools can’t be added,
    deploy WAF, anomaly detection, or rate limits to match risk posture.

---

## Development-Phase Principles

### New Code

14. **Secure-by-Default Templates** – New repos **MUST** bootstrap from approved
    templates with OWASP Top-10 mitigations pre-wired.
15. **Stateless First** – Components **SHOULD** avoid local state; if
    unavoidable, they **MUST** persist in managed durable storage.
16. **Feature Flags over Forks** – Long-lived branches > 14 days are disallowed;
    use feature toggles instead.
17. **API Contract Tests** – Public APIs **MUST** include consumer-driven
    contract tests committed alongside code.

---

### Existing Code

18. **Upgrade-on-Touch** – Any code touched **SHOULD** move to a supported LTS
    runtime, add missing unit tests, and replace deprecated libs.
19. **Refactor via Strangler** – When adding features, prefer building new
    modules that wrap/replace legacy endpoints incrementally.
20. **Technical-Debt Ledger** – Every legacy repo **MUST** keep a debt log;
    high-impact items enter the sprint backlog within one sprint.

---

## Quality-Assurance-Phase Principles

### New Code

21. **Test Pyramid Guardrails** – Aim for >= 80% unit, >= 15% integration, <= 5%
    e2e by count; gates enforce on PR.
22. **Tagged Tests** – Tests **MUST** declare tags (`unit`, `integration`,
    `performance`, `security`) so CI jobs select appropriately.
23. **Synthetic & Pseudonymised Data** – QA pipelines **MUST NOT** use live PII;
    generate synthetic or tokenised data instead.
24. **Performance Baselines** – New services **SHOULD** include load tests with
    SLO thresholds; regressions block merge.
25. **Flaky-Test Quarantine** – Tests failing > 3% of runs automatically move to
    quarantine and open a ticket within 24 h.

---

### Legacy Code

26. **Coverage-Growth Mandate** – When fixing a bug, add tests that reproduce
    it; overall coverage **SHOULD** trend upward quarter-on-quarter.
27. **Runtime Compensating Checks** – If shift-left tools can’t be added, deploy
    runtime guards (WAF, anomaly detection) offering equivalent risk coverage.
28. **Regression Test Archive** – Critical bugs in legacy systems **MUST**
    result in a regression test stored under `/tests/regression/**`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spallempati) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
