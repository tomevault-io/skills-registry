---
name: verilog-vhdl-design
description: Verilog VHDL Design workflows for quantitative research, implementation, and production controls. use when tasks involve synthesizable rtl modules, deterministic state machines, and testbench coverage. Use when this capability is needed.
metadata:
  author: ghostof0days
---

# Verilog VHDL Design
## objective
Execute verilog vhdl design work with reproducible research, explicit controls, and deployable outputs.

## workflow
1. define end-to-end latency budget and deterministic performance targets.
2. instrument each stage from feed ingress to order egress.
3. optimize kernel, memory, and network path for tail-latency reduction.
4. stress packet bursts, failovers, and capacity saturation scenarios.
5. promote only after reproducible latency and recovery behavior is verified.

## required diagnostics
- stage-level p50, p99, and p999 latency decomposition.
- jitter and throughput stability under sustained burst load.
- packet-loss recovery time and replay correctness.
- resource saturation signals before service-level breach.
- functional coverage gaps on edge-case packet sequences
- timing and reset behavior under burst conditions

## risk controls
- enforce hard latency and packet-loss service objectives.
- enforce automatic failover and load-shedding thresholds.
- enforce runbooks for exchange-connectivity incidents.

## outputs
- run `python scripts/verilog_vhdl_design_diagnostics.py input.csv --output diagnostics.json` and keep the json artifact.
- write an implementation memo using `references/verilog-vhdl-design-playbook.md` with assumptions, tests, limits, and rollout plan.

## resources
- use `scripts/verilog_vhdl_design_diagnostics.py` for deterministic diagnostics.
- use `references/verilog-vhdl-design-playbook.md` for the domain-specific checklist and delivery structure.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ghostof0days) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
