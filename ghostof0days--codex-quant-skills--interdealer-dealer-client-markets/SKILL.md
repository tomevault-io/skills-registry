---
name: interdealer-dealer-client-markets
description: Interdealer Dealer Client Markets workflows for quote quality, venue segmentation, and execution performance across interdealer and dealer-to-client microstructures. use when tasks involve rfq/rfs behavior, quote-response analytics, market-structure comparison, or production monitoring of dealer flow quality. Use when this capability is needed.
metadata:
  author: ghostof0days
---

# Interdealer Dealer Client Markets
## objective
Execute interdealer and dealer-client market workflows with reproducible diagnostics and production controls.

## workflow
1. define venue segmentation, protocol assumptions, and execution benchmarks.
2. ingest quote, response-time, hit-ratio, and fill-quality data by market channel.
3. compare interdealer versus dealer-to-client behavior under normal and stressed conditions.
4. stress execution with volatility shocks, quote-withdrawal events, and liquidity fragmentation.
5. release only when venue-routing and quote-quality metrics are stable and within limits.

## required diagnostics
- quote spread and response-latency comparison by channel.
- hit-ratio and reject-rate behavior by counterparty cohort.
- slippage and fill-quality attribution for rfq and streamed quotes.
- information-leakage proxy behavior around quote requests.
- channel-level liquidity resilience during stress windows.

## risk controls
- enforce venue concentration and counterparty dependence limits.
- enforce hard thresholds for response latency and reject-rate spikes.
- enforce alerting for persistent quote-quality degradation.

## outputs
- run `python scripts/interdealer_dealer_client_markets_diagnostics.py input.csv --output diagnostics.json` and keep the json artifact.
- write an implementation memo using `references/interdealer-dealer-client-markets-playbook.md` with assumptions, tests, limits, and rollout plan.

## resources
- use `scripts/interdealer_dealer_client_markets_diagnostics.py` for deterministic diagnostics.
- use `references/interdealer-dealer-client-markets-playbook.md` for the domain checklist and delivery structure.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ghostof0days) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
