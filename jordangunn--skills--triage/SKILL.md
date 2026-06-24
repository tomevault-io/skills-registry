---
name: doctor-triage
description: | Use when this capability is needed.
metadata:
  author: jordangunn
---

# doctor-triage

Perform **breadth-first hypothesis surfacing and prioritization** across all ownership zones.

## Purpose

Assume the problem may live in a layer the user is ignoring. Prefer "possible and boring" over "clever and narrow."

This is **not** diagnosis and **not** investigation.

## Epistemic Stance

- The problem may originate outside the layer where symptoms manifest
- Early confidence is a warning sign, not a virtue
- Multiple hypotheses are better than one "obvious" answer

## Scope of Consideration (Always Include)

- Backend application code
- Frontend build/runtime
- CI/CD pipelines
- Container build & images
- Kubernetes manifests (ingress, services, probes, RBAC, secrets)
- Cloud infrastructure (IaC, identity, networking, storage)
- Configuration & environment variables
- Dependencies and versions

## You MUST

- Enumerate plausible causes across ALL zones
- Rank them by likelihood with explanation
- Cite lightweight evidence pointers (file paths, grep hits, manifest locations)
- Explicitly call out assumptions under dispute
- Recommend which suspect(s) merit focused exam next

## You MUST NOT

- Investigate deeply
- Open large files unnecessarily
- Collapse uncertainty into a single answer
- Propose fixes

## Output

A completed **Triage Report** using the template at `../.resources/assets/TRIAGE_REPORT.md`.

## Scripts

- `evidence.sh` — Lightweight grep-based evidence pointer gathering

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jordangunn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
