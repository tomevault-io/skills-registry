---
name: hvm-runtime
description: HVM Runtime Skill Use when this capability is needed.
metadata:
  author: plurigrid
---


# hvm-runtime Skill


> *"Optimal reduction at the speed of light. Interaction nets meet GPUs."*

## Overview

**HVM Runtime** (Higher-order Virtual Machine) implements massively parallel functional computation using interaction nets. Compiles functional code to GPU-accelerated graph reduction.

## GF(3) Role

| Aspect | Value |
|--------|-------|
| Trit | +1 (PLUS) |
| Role | GENERATOR |
| Function | Generates optimal parallel reductions |

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                      HVM RUNTIME                                │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Source Code      Compiler       Runtime        Output         │
│  (+1 GEN)        (0 COORD)      (+1 GEN)       (result)        │
│      │               │              │               │          │
│      ▼               ▼              ▼               ▼          │
│  ┌───────┐      ┌────────┐    ┌──────────┐   ┌─────────┐      │
│  │ Bend  │─────►│ Compile│───►│ Parallel │──►│ Normal  │      │
│  │ Lang  │      │ to Net │    │ Reduce   │   │ Form    │      │
│  └───────┘      └────────┘    └──────────┘   └─────────┘      │
│                                    │                           │
│                     ┌──────────────┼──────────────┐            │
│                     ▼              ▼              ▼            │
│                   GPU            CUDA          Metal           │
│                 Threads         Cores         Shaders          │
│                                                                │
└─────────────────────────────────────────────────────────────────┘
```

## Interaction Net Compilation

```haskell
-- Bend source (functional language for HVM)
def fib(n):
  match n:
    0: 0
    1: 1
    _: fib(n-1) + fib(n-2)

-- Compiles to interaction net nodes:
-- λ, App, Dup, Era, Sup, Con
```

## Node Types

```rust
// HVM interaction net nodes
enum Node {
    // Lambda abstraction
    Lam {
        body: Port,
        var: Port
    },

    // Application
    App {
        func: Port,
        arg: Port
    },

    // Duplicator (for lazy sharing)
    Dup {
        label: u32,
        left: Port,
        right: Port
    },

    // Eraser (garbage collection)
    Era,

    // Superposition (parallel branches)
    Sup {
        label: u32,
        left: Port,
        right: Port
    },

    // Constructor (data types)
    Con {
        tag: u32,
        fields: Vec<Port>
    },
}
```

## Parallel Reduction

```rust
// GPU-parallel interaction
fn parallel_reduce(net: &mut Net, gpu: &Gpu) {
    loop {
        // Find all active pairs (redexes)
        let redexes = find_redexes(net);

        if redexes.is_empty() {
            break;  // Normal form reached
        }

        // Reduce all in parallel on GPU
        gpu.dispatch(redexes.len(), |i| {
            let (a, b) = redexes[i];
            interact(net, a, b);
        });
    }
}

// Core interaction rules
fn interact(net: &mut Net, a: Node, b: Node) {
    match (a.tag, b.tag) {
        // β-reduction: (λx.body) @ arg → body[x := arg]
        (LAM, APP) => {
            link(a.body, b.arg);
            link(a.var, b.func);
        },

        // Duplication: Dup @ λ → λ₁, λ₂
        (DUP, LAM) => {
            let lam1 = new_node(LAM);
            let lam2 = new_node(LAM);
            // ... wire up
        },

        // Erasure: Era @ any → nothing
        (ERA, _) => {
            // Node b is garbage collected
        },

        // Superposition annihilation
        (SUP, SUP) if a.label == b.label => {
            link(a.left, b.left);
            link(a.right, b.right);
        },

        // Superposition commutation
        (SUP, SUP) => {
            // Create 4 new nodes, rewire
        },
    }
}
```

## Optimal Sharing

```
Traditional:                      HVM (Optimal):

  f (expensive)                     f (expensive)
    │                                   │
    ├───► result1                   ┌───┴───┐
    │                               │  Dup  │
    └───► result2                   └───┬───┘
                                        │
Computes f twice!               ├───► result1 (shared!)
                                └───► result2
```

## Bend Language Examples

```python
# Parallel map (auto-parallelizes)
def pmap(f, xs):
  fold xs:
    List.nil: List.nil
    List.cons: List.cons(f(xs.head), pmap(f, xs.tail))

# Parallel reduce
def sum(xs):
  fold xs:
    List.nil: 0
    List.cons: xs.head + sum(xs.tail)

# GPU-accelerated recursion
def parallel_fib(n):
  bend val = 0, i = 0:
    when i < n:
      left = fork(val + 1, i + 1)
      right = fork(val, i + 2)
      left + right
    else:
      val
```

## Performance Characteristics

| Operation | Complexity | GPU Speedup |
|-----------|------------|-------------|
| β-reduction | O(1) | N/A |
| Duplication | O(size) | 10-100x |
| Parallel map | O(n/cores) | 100-1000x |
| Fold | O(log n) | 10-100x |

## GF(3) Integration

```python
class GF3HVMRuntime:
    """HVM runtime with GF(3) node classification."""

    TRIT = 1  # GENERATOR role

    # Node roles in GF(3)
    NODE_TRITS = {
        'LAM': 1,   # GENERATOR: creates values
        'APP': 0,   # COORDINATOR: routes computation
        'DUP': 0,   # COORDINATOR: manages sharing
        'ERA': -1,  # VALIDATOR: garbage collection
        'CON': 1,   # GENERATOR: constructs data
        'SUP': 0,   # COORDINATOR: parallel branches
    }

    def verify_conservation(self, net):
        """Check GF(3) conservation after reduction."""
        trit_sum = sum(self.NODE_TRITS[n.tag] for n in net.nodes)
        return trit_sum % 3 == 0
```

## GF(3) Triads

```
hvm-runtime (+1) ⊗ interaction-nets (0) ⊗ linear-logic (-1) = 0 ✓
hvm-runtime (+1) ⊗ datalog-fixpoint (0) ⊗ type-checker (-1) = 0 ✓
hvm-runtime (+1) ⊗ triadic-skill-orchestrator (0) ⊗ narya-proofs (-1) = 0 ✓
```

## Commands

```bash
# Run Bend program
bend run program.bend

# Compile to HVM
bend compile program.bend -o program.hvm

# GPU execution
bend run program.bend --gpu cuda

# Profile reduction
bend run program.bend --profile

# Show interaction net
bend debug program.bend --show-net
```

---

**Skill Name**: hvm-runtime
**Type**: Parallel Computation / Lambda Calculus
**Trit**: +1 (PLUS - GENERATOR)
**GF(3)**: Generates optimal parallel reductions


## Scientific Skill Interleaving

This skill connects to the K-Dense-AI/claude-scientific-skills ecosystem:

### Graph Theory
- **networkx** [○] via bicomodule
  - Universal graph hub

### Bibliography References

- `general`: 734 citations in bib.duckdb

## Cat# Integration

This skill maps to Cat# = Comod(P) as a bicomodule in the Prof home:

```
Trit: 0 (ERGODIC)
Home: Prof (profunctors/bimodules)
Poly Op: ⊗ (parallel composition)
Kan Role: Adj (adjunction bridge)
```

### GF(3) Naturality

The skill participates in triads where:
```
(-1) + (0) + (+1) ≡ 0 (mod 3)
```

This ensures compositional coherence in the Cat# equipment structure.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/plurigrid) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
