---
name: pandapower
description: Progressive-disclosure workflow for pandapower studies. Use when the user asks to load or build a pandapower network, inspect network data, run AC power flow, or perform contingency screening, and when the agent should expose base-case power flow before advanced outage studies. Use when this capability is needed.
metadata:
  author: power-agent
---

# pandapower workflow

Start with the network model and a clean base-case solve. Use contingency analysis only after the steady-state case is credible.

## Default tool ladder
1. `load_network(file_path)` or `create_empty_network()`
2. `get_network_info()` to inspect buses, lines, transformers, generators, loads, and switches.
3. `run_power_flow(...)` to establish the base operating point.
4. `run_contingency_analysis(...)` only after the base case solves and the monitoring limits are clear.

## Working rules
- Do not run contingencies before checking the base-case voltages and loading.
- Use pandapower for AC feasibility and fast screening, then escalate only when the question requires more.
- Use `voltage-violation-mitigation`, `thermal-overload-mitigation`, or `contingency-mitigation` after violations are identified.

## Local assets in this repo
- `pandapower/case39.json` for a ready test network.
- `pandapower/scripts/network_analysis.py` for structured base-case review.
- `pandapower/scripts/contingency_analysis.py` for local N-1 screening.

## Deliver
- The network loaded and whether the base case converged.
- The key voltage or loading findings.
- Whether contingency or mitigation work is now justified.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/power-agent) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
