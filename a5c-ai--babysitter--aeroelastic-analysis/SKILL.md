---
name: aeroelastic-analysis
description: Skill for flutter, divergence, and aeroelastic response analysis Use when this capability is needed.
metadata:
  author: a5c-ai
---

# Aeroelastic Analysis Skill

## Purpose
Provide comprehensive aeroelastic analysis capabilities for flutter prevention, divergence assessment, and dynamic response prediction.

## Capabilities
- Flutter analysis (p-k, PKNL methods)
- Divergence speed determination
- Control reversal analysis
- Gust response analysis
- Ground vibration test correlation
- Aeroelastic tailoring assessment
- Structural coupling analysis
- Flight envelope clearance documentation

## Usage Guidelines
- Validate structural modes against GVT data before flutter analysis
- Use appropriate aerodynamic theories for the flight regime
- Include control system effects in flutter predictions
- Apply required flutter margins per certification requirements
- Consider fuel state and payload variations in analysis matrix
- Document all modeling assumptions and uncertainties

## Dependencies
- MSC NASTRAN (SOL 144/145/146)
- ZAERO
- FlightLoads

## Process Integration
- AE-010: Aeroelastic Analysis

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/a5c-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
