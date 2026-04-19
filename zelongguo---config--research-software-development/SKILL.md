---
name: research-software-development
description: Skill for research-oriented scientific software development, balancing reproducibility, correctness, and long-term maintainability, with selective engineering rigor. Use when this capability is needed.
metadata:
  author: zelongguo
---

# Research Software Development Skill

This skill provides guidance for **research-driven software development**, where the primary goals are **scientific correctness, reproducibility, and clarity**, while applying **engineering rigor selectively** when software evolves toward long-lived tools, shared codebases, or community-facing packages.

It draws inspiration from **Clean Architecture** and **Domain-Driven Design (DDD)**, but adapts them to the realities of scientific computing, numerical modeling, and exploratory research workflows (e.g., InSAR/GNSS processing, geophysical modeling, and data analysis pipelines).

## Guiding Philosophy

* **Science first, engineering second**: prioritize correctness, traceability, and interpretability over premature abstraction.
* **Reproducibility over cleverness**: code should make experiments easy to reproduce and audit.
* **Progressive rigor**: notebooks and scripts are acceptable early; architectural structure increases as code matures.
* **Models are research assets**: algorithms, physical models, and inversion logic are more valuable than infrastructure glue.


## Code Style Rules

### General Principles

* **Early return pattern**: prefer early returns over deeply nested conditionals to improve readability.
* Avoid copy-paste logic in scientific workflows; extract reusable functions for (for example):

  * forward models
  * kernels / Green's functions
  * misfit and regularization terms
* Functions exceeding **80 lines** should be decomposed when possible.
* Files exceeding **200 lines** should be split *only if* it improves conceptual clarity (not just to satisfy style).
* Prefer **pure functions** for scientific computation (inputs → outputs, no hidden state).


### Numerical and Scientific Code Practices

* Make **units, coordinate systems, and conventions explicit** in names and documentation.
* Avoid magic numbers; define physical constants and hyperparameters clearly.
* Favor readability over micro-optimizations unless the code is on a proven performance-critical path.
* Numerical stability and physical interpretability take precedence over abstraction purity.


## Library-First (but Research-Aware) Approach

### Preferred Strategy

* **ALWAYS check existing scientific libraries before writing custom implementations**:

  * numerical linear algebra (e.g., BLAS/LAPACK wrappers)
  * optimization and inversion libraries
  * geospatial and remote-sensing toolkits
* Reuse well-tested libraries for:

  * IO, file formats, coordinate transforms
  * plotting and visualization
  * parallelization and acceleration

### When Custom Code *Is* Justified

Custom implementations are appropriate when:

* The logic encodes **domain-specific physical models** (e.g., fault slip, viscoelastic relaxation).
* Existing libraries do not support required assumptions or geometries.
* The code represents a **research contribution** rather than infrastructure.
* Full transparency is required for peer review and reproducibility.
* Performance-critical kernels require tailored optimization.

> Scientific novelty is a valid reason to write custom code.


## Architecture and Structure

### Research-Oriented Clean Architecture

* Keep **scientific core logic** independent of:

  * plotting frameworks
  * file system layout
  * command-line interfaces
* Separate clearly:

  * physical / mathematical models
  * numerical solvers
  * data preparation and visualization

Typical layering (conceptual, not rigid):

* **Domain layer**: equations, physical assumptions, forward/inverse models
* **Application layer**: experiment setup, parameter sweeps, inversion workflows
* **Infrastructure layer**: IO, plotting, parallel execution, external tools


### Naming Conventions (Critical for Research Code)

* **AVOID** vague names: `utils`, `helpers`, `misc`, `test2`
* **USE** names that encode scientific meaning:

  * `TriangularDislocationKernel`
  * `AfterSlipInversion`
  * `ViscoelasticRelaxationModel`
* Prefer names that would still make sense **5 years later or to another researcher**.


## Separation of Concerns (Applied Pragmatically)

* Do NOT mix physical equations with plotting logic.
* Do NOT embed hard-coded experiment parameters inside core model functions.
* Keep inversion configuration separate from inversion algorithms.
* Allow notebooks/scripts to orchestrate experiments, but keep them thin.

> Scripts orchestrate; libraries explain.


## Anti-Patterns to Avoid in Research Software

* Re-implementing standard numerical methods without justification.
* Over-engineering early-stage exploratory code.
* Monolithic scripts that mix:

  * data loading
  * modeling
  * inversion
  * visualization
* "One-off" hacks silently becoming production research code.

Remember:

> Undocumented scientific code is irreproducible science.


## Code Quality and Reproducibility

* Ensure deterministic behavior where possible (random seeds, fixed solvers).
* Use clear error messages for invalid physical assumptions or parameter ranges.
* Keep functions under **50 lines** when feasible, but allow exceptions for mathematically cohesive blocks.
* Prefer explicit over implicit behavior, even if slightly verbose.
* Minimal but meaningful documentation:

  * what problem is solved
  * what assumptions are made
  * what each parameter represents


## Transition to Engineering-Grade Software

When research code evolves into:

* shared group tools
* open-source packages
* long-term modeling frameworks

then progressively introduce:

* stronger module boundaries
* stricter testing
* clearer public APIs
* more conventional Clean Architecture discipline


## Core Principle

> In research software, **clarity and correctness define quality**; architecture exists to preserve them as complexity grows.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zelongguo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
