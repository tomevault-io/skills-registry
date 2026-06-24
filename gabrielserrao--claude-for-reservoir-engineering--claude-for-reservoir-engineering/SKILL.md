---
name: reservoir-engineering
description: Use for reservoir engineering workflows, pyResToolbox calculations, pyrestoolbox-mcp tools, PVT, DCA, material balance, nodal analysis, relperm, brine, geomechanics, and simulation input generation. Use when this capability is needed.
metadata:
  author: gabrielserrao
---

# Reservoir Engineering Skill

Use pyResToolbox or pyrestoolbox-mcp for engineering correlations whenever possible. Avoid hand-rolling PVT, VLP, material balance, relative permeability, or DCA formulas unless the exercise explicitly asks for implementation.

## Common Tool Families

- Oil PVT: bubble point, solution GOR, oil FVF, density, compressibility, viscosity.
- Gas PVT: pseudocritical properties, Z-factor, Bg, density, viscosity, compressibility, pseudopressure.
- DCA: Arps exponential/harmonic/hyperbolic, Duong, EUR, forecast, uptime handling.
- Material balance: gas P/Z, oil Havlena-Odeh, aquifer support, drive indices.
- Nodal/IPR/VLP: inflow curves, outflow curves, operating point, VFP tables.
- Simtools: SWOF/SGOF/SGWFN, PVTO/PVDO/PVDG/PVTW, AQUTAB, Rachford-Rice flash.
- Layer: Lorenz coefficient and permeability distributions.
- Brine/CO2: brine properties and CO2-brine mutual solubility.

## Parameter Pitfalls

- Oil tools use `sg_g` for gas gravity.
- Gas tools use `sg` for gas gravity.
- Inflow tools use `psd`, not `pwf`.
- Gas Z-factor uses `method`; many other gas property tools use `zmethod`.
- Oil bubble point methods include `STAN`, `VALMC`, and `VELAR`.
- Gas Z-factor methods include `DAK`, `HY`, `WYW`, and `BUR`.
- Relative permeability table types include `SWOF`, `SGOF`, and `SGWFN`.

## Output Standard

Every answer should include:

- Inputs and units.
- Correlation or method used.
- Result table or scalar result.
- Engineering sanity check.
- Any applicability limits or assumptions.

---
> Source: [gabrielserrao/Claude-for-reservoir-engineering](https://github.com/gabrielserrao/Claude-for-reservoir-engineering) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-19 -->
