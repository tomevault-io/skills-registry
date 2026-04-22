---
name: hls-fpga
description: HLS FPGA workflows for converting C/C++ algorithm kernels into synthesizable hardware with pragma tuning, throughput modeling, and equivalence validation. use when tasks involve high-level synthesis productivity flows rather than handwritten rtl design. Use when this capability is needed.
metadata:
  author: ghostof0days
---

# HLS FPGA
## objective
Translate algorithmic kernels into efficient FPGA implementations using high-level synthesis and systematic pragma optimization.

## workflow
1. define kernel interfaces, throughput target, and initiation interval goals.
2. apply hls pragmas for pipelining, unrolling, and memory partitioning.
3. synthesize and compare latency-resource tradeoffs across pragma sets.
4. validate c-simulation, co-simulation, and post-synthesis equivalence.
5. promote hls design only after stable qos and verification coverage.

## required diagnostics
- initiation interval and loop-latency target attainment.
- pragma sensitivity on area-throughput frontier.
- c-model versus rtl co-simulation mismatch rate.
- memory bandwidth bottleneck diagnostics.
- build-to-build reproducibility of synthesis outcomes.

## risk controls
- enforce equivalence checks before bitstream handoff.
- enforce resource headroom guardrails per deployment target.
- enforce deterministic build environment pinning.

## outputs
- run `python scripts/hls_fpga_diagnostics.py input.csv --output diagnostics.json` and keep the json artifact.
- write an implementation memo using `references/hls-fpga-playbook.md` with assumptions, tests, limits, and rollout plan.

## resources
- use `scripts/hls_fpga_diagnostics.py` for deterministic diagnostics.
- use `references/hls-fpga-playbook.md` for the domain checklist and delivery structure.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ghostof0days) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
