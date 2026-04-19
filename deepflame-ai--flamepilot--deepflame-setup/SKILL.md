---
name: deepflame-case-setup
description: Set up DeepFlame combustion cases with chemistry, boundary conditions, and solver configuration. Use for combustion simulations involving dfLowMachFoam, dfSprayFoam, dfHighSpeedFoam. Use when this capability is needed.
metadata:
  author: deepflame-ai
---

# DeepFlame Case Setup

## TUTORIAL-FIRST WORKFLOW

**ALWAYS start by examining DeepFlame example cases:**
- Use `list` and `read` tools on deepflame/examples/ directory
- Grep for: `dfLowMachFoam`, `dfSprayFoam`, `dfHighSpeedFoam`, `CanteraTorchProperties`
- Copy and adapt existing cases rather than building from scratch

## CRITICAL SETUP STEPS

### 1. CanteraTorchProperties Configuration
**Grep patterns:** `CanteraMechanismFile`, `torchModel`, `loadBalancing`
- File: `constant/CanteraTorchProperties`
- **ABSOLUTE PATHS ONLY** for mechanism files (e.g., `/full/path/mechanism.yaml`)
- Never use relative paths - causes simulation failures

### 2. Species Configuration
**Grep patterns:** `Ydefault`, `species`, `mass fraction`
- `0/Ydefault` file provides default mass fractions for chemical species
- Essential for combustion cases with multiple species
- Usually set to 0 for species not explicitly defined

### 3. Boundary Conditions
**Grep patterns:** `inlet`, `outlet`, `wall`, `fixedValue`, `zeroGradient`
- Inlet: `fixedValue` for velocity, temperature, species mass fractions
- Outlet: `zeroGradient` or `inletOutlet`
- Walls: `fixedValue` for temperature, `noSlip` for velocity

### 4. Solver Selection
**Grep patterns:** `dfLowMachFoam`, `dfSprayFoam`, `dfHighSpeedFoam`
- `dfLowMachFoam`: Low Mach number combustion
- `dfSprayFoam`: Spray combustion with Lagrangian particles
- `dfHighSpeedFoam`: High-speed combustion flows

## SETUP VERIFICATION PROTOCOL

**ALWAYS set `stopAt` in `system/controlDict` to `"writeNow"` for initial testing:**
- Ensures setup correctness before full production runs
- Test mesh generation, boundary conditions, initial convergence
- After successful test, set `stopAt = "endTime"` for production

## TROUBLESHOOTING PATTERNS

**Grep for error patterns:**
- `CanteraMechanismFile not found` → Check absolute path in CanteraTorchProperties
- `species not found` → Verify Ydefault file and species names
- `boundary condition error` → Check inlet/outlet/wall BC consistency
- `loadBalancing failed` → Review CanteraTorchProperties loadBalancing section

## ADDITIONAL RESOURCES

For detailed configuration options, search DeepFlame documentation and examples:
- Grep docs/ for: `combustion`, `chemistry`, `turbulence`, `boundary conditions`
- Examine deepflame/examples/ cases for complete working configurations
- Use paper_analysis tool on combustion CFD papers for advanced techniques

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/deepflame-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
