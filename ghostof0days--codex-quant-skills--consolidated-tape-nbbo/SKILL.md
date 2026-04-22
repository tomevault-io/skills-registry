---
name: consolidated-tape-nbbo
description: Consolidated Tape NBBO workflows for sip/direct-feed consolidation, national best bid and offer reconstruction, and trade-through surveillance. use when tasks involve cross-venue quote consolidation, protected quote logic, nbbo quality controls, or production monitoring of tape integrity. Use when this capability is needed.
metadata:
  author: ghostof0days
---

# Consolidated Tape NBBO
## objective
Build and monitor consolidated tape and nbbo pipelines with latency-aware validation and market-structure controls.

## workflow
1. define venue universe, quote protection rules, and timestamp precedence conventions.
2. normalize sip and direct feeds into a unified quote and trade schema.
3. reconstruct nbbo snapshots and track locked or crossed market states.
4. detect trade-through events and attribute them to latency, stale quotes, or routing errors.
5. release only when quote quality and reconciliation diagnostics remain within thresholds.

## required diagnostics
- nbbo spread stability and outlier frequency by symbol and session.
- locked and crossed market rate with root-cause attribution.
- sip-versus-direct latency differential and drift.
- trade-through incidence versus protected quote state.
- quote staleness and missing-venue coverage metrics.

## risk controls
- enforce hard thresholds on crossed-market and trade-through rates.
- enforce stale-quote quarantine and fallback routing flags.
- enforce feed-health escalation when venue coverage drops.

## outputs
- run `python scripts/consolidated_tape_nbbo_diagnostics.py input.csv --output diagnostics.json` and keep the json artifact.
- write an implementation memo using `references/consolidated-tape-nbbo-playbook.md` with assumptions, tests, limits, and rollout plan.

## resources
- use `scripts/consolidated_tape_nbbo_diagnostics.py` for deterministic diagnostics.
- use `references/consolidated-tape-nbbo-playbook.md` for the domain checklist and delivery structure.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ghostof0days) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
