---
name: openfoam-case-setup
description: Set up complete OpenFOAM CFD cases with proper boundary conditions, solver configuration, and physics modeling. Use for non-reacting flows, incompressible flows, and general CFD simulations. Use when this capability is needed.
metadata:
  author: deepflame-ai
---

# OpenFOAM Case Setup

This skill provides specialized guidance for setting up OpenFOAM CFD cases, including:

## Key Features
- Standard OpenFOAM case structure setup
- Solver selection guidance (simpleFoam, pimpleFoam, icoFoam, etc.)
- Turbulence model configuration
- Boundary condition setup for various physics
- Mesh generation workflows
- Example case adaptation strategies

## Common Use Cases
- Setting up new CFD simulations
- Configuring turbulence models
- Optimizing solver settings
- Troubleshooting case setup issues
- Adapting tutorial cases for specific problems

## Instructions

### Tutorial-First Setup Workflow
**ALWAYS start with tutorial cases** - they provide validated, working configurations

1. **Find Match**: Use OpenFOAM keywords to search tutorials
   ```
   # By solver
   grep(pattern='simpleFoam|pimpleFoam|rhoSimpleFoam', path=config.openfoam.tutorials_path, include='system/controlDict')

   # By turbulence model
   grep(pattern='kEpsilon|kOmegaSST|kOmega', path=config.openfoam.tutorials_path, include='constant/momentumTransport')

   # By physics/problem type
   grep(pattern='incompressible|compressible|turbulent|heatTransfer', path=config.openfoam.tutorials_path, include='README*')
   ```
2. **Copy Base**: `cp -r /tutorials/path/case/* ./`
3. **Adapt**: Modify geometry, BCs, physics from working baseline
4. **Troubleshoot**: Compare with tutorial when issues arise

### Tutorial-Based Troubleshooting
**When cases fail to run:**
1. Find similar working tutorial case using specific keywords:
   ```
   # For divergence issues
   grep(pattern='simpleFoam|pimpleFoam', path=config.openfoam.tutorials_path, include='system/controlDict')

   # For boundary condition problems
   grep(pattern='fixedValue|zeroGradient|noSlip', path=config.openfoam.tutorials_path, include='0/*')

   # For convergence issues
   grep(pattern='residualControl|tolerance', path=config.openfoam.tutorials_path, include='system/fvSolution')
   ```

2. Compare ALL settings (controlDict, fvSchemes, fvSolution, BCs, mesh)
3. Use tutorial values as starting point - modify incrementally
4. Reference tutorial outputs for expected behavior

**Common Fixes from Tutorials:**
- **Divergence**: Copy discretization schemes from tutorial fvSchemes
- **Boundary Errors**: Use tutorial BC patterns from 0/ directory
- **Convergence Issues**: Adopt tutorial solver tolerances from fvSolution
- **Mesh Problems**: Reference tutorial mesh settings from blockMeshDict

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/deepflame-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
