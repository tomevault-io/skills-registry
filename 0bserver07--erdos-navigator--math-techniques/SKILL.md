---
name: math-techniques
description: Mathematical proof techniques library for working on Erdős problems. Use when attempting to solve combinatorics, number theory, graph theory, or geometry problems. Includes techniques from successful AI solutions. Use when this capability is needed.
metadata:
  author: 0bserver07
---

# Mathematical Techniques Library

A curated collection of proof techniques relevant to Erdős problems, organized by problem domain.

## Quick Reference

| Domain | Common Techniques |
|--------|-------------------|
| Number Theory | Pell equations, quadratic forms, modular arithmetic |
| Combinatorics | Pigeonhole, probabilistic method, counting arguments |
| Graph Theory | Ramsey theory, chromatic bounds, incidence geometry |
| Geometry | Lattice constructions, distance sets, polynomial methods |

## Technique Files

For detailed technique descriptions, see:
- [NUMBER_THEORY.md](NUMBER_THEORY.md) - Prime sequences, Diophantine equations
- [COMBINATORICS.md](COMBINATORICS.md) - Counting, Ramsey, extremal
- [GRAPH_THEORY.md](GRAPH_THEORY.md) - Coloring, distances, matchings
- [GEOMETRY.md](GEOMETRY.md) - Point sets, distances, lattices
- [LITERATURE.md](LITERATURE.md) - Key papers and reductions

## Problem-Solving Workflow

### Phase 1: Problem Analysis
1. Parse the LaTeX statement carefully
2. Identify the problem domain (tags)
3. Check if formalized in Lean (lean_url field)
4. Read original Erdős paper references

### Phase 2: Literature Check
1. Search for problem number in academic databases
2. Check Terence Tao's wiki for AI progress
3. Look for related OEIS sequences
4. Review comments on erdosproblems.com

### Phase 3: Technique Selection
Based on problem type:
- **Existence proofs**: Probabilistic method, constructions
- **Bounds**: Incidence geometry, polynomial methods
- **Sequences**: Pell equations, growth rate analysis
- **Graphs**: Ramsey theory, chromatic number bounds

### Phase 4: Solution Attempt
1. Try simplest applicable technique first
2. Look for reductions to known results
3. Check for counterexamples if conjecture
4. Verify small cases computationally

### Phase 5: Verification
1. Check solution addresses intended interpretation
2. Search literature for existing solutions
3. Verify with formal tools if possible
4. Have human expert review

## Success Patterns from AI Solutions

### Erdős-652 (Distinct Distances)
**Technique**: Reduction to Pach-Sharir incidence bounds
**Pattern**: Connect discrete geometry to incidence theory

### Erdős-1051 (Irrationality of Series)
**Technique**: Mahler's criterion, growth rate analysis
**Pattern**: Establish contradiction via asymptotic bounds

### Erdős-654 (Four Points No Circle)
**Technique**: Axis-aligned construction with prime powers
**Pattern**: Use number-theoretic constraints for geometric problems

### Erdős-935 (Powerful Parts)
**Technique**: Pell equation solutions + Dirichlet's theorem
**Pattern**: Construct specific sequences via Diophantine equations

### Erdős-397 (Central Binomial Products)
**Technique**: Explicit parametric family construction
**Pattern**: Find algebraic identity yielding infinite solutions

## Warning Signs

- Problem seems "too easy" → likely solved or misinterpreted
- Solution doesn't use domain-specific tools → check interpretation
- Can't find any related literature → problem may be stated incorrectly
- Proof works for all cases trivially → definitional ambiguity likely

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/0bserver07) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
