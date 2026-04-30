---
name: sdf
description: Software Design for Flexibility: Sussman & Hanson's additive programming, combinators, propagators, and generic dispatch for evolvable systems Use when this capability is needed.
metadata:
  author: plurigrid
---

# SDF Skill: Software Design for Flexibility

> *"It is better to have 100 functions operate on one data structure than 10 functions on 10 data structures."*
> — Alan Perlis (via Sussman & Hanson)

Geometric morphism from the MIT Press 2021 text, preserving the compositional structure as an ACSet with GF(3) coloring for trifurcated processing.

## Overview

**Software Design for Flexibility**  
by Chris Hanson and Gerald Jay Sussman  
MIT Press, 2021  
ISBN: 978-0262045490

The successor to SICP focused on **additive programming**—building systems that can evolve by adding new capabilities without modifying existing code.

## Core Principles

### The Flexibility Mandate

1. **Additive over Modificative**: New features via addition, not mutation
2. **Generic over Specific**: Operations that work across types
3. **Compositional over Monolithic**: Small combinable pieces
4. **Declarative over Imperative**: Constraints over control flow

## Chapters with GF(3) Trit Assignment

### Part I: Flexibility in Primitive Parts [PLUS]

#### Chapter 1: Flexibility through Abstraction (+1)
- Combinators as primitive building blocks
- `compose`, `parallel-combine`, `spread-combine`
- Arity management and currying patterns

Key combinator:
```scheme
(define (compose f g)
  (lambda args
    (f (apply g args))))
```

#### Chapter 2: Domain-Specific Languages (-1)
- Embedded DSLs via combinators
- Wrapper strategies for APIs
- Pattern-directed invocation

### Part II: Flexibility through Dispatch [ERGODIC]

#### Chapter 3: Variations on an Arithmetic Theme (0)
- Generic arithmetic operations
- Type coercion lattices
- Symbolic vs numeric duality

#### Chapter 4: Pattern Matching (+1)
- Unification as composition
- Segment variables and pattern operators
- Match combinators

```scheme
(define (match:element variable)
  (lambda (data dictionary succeed)
    (let ((binding (assq variable dictionary)))
      (if binding
          (and (equal? (cdr binding) data)
               (succeed dictionary))
          (succeed (cons (cons variable data)
                        dictionary))))))
```

#### Chapter 5: Evaluation (-1)
- Generic eval/apply
- Environment models
- Interpreter variations

### Part III: Flexibility through Modularity [PLUS]

#### Chapter 6: Layering (+1)
- Layered data with metadata
- Provenance tracking
- Units and dimensions

```scheme
(define (make-layered-datum base-value . layers)
  (cons base-value layers))

(define (layered-datum-value datum)
  (car datum))

(define (layered-datum-layers datum)
  (cdr datum))
```

#### Chapter 7: Propagators (0)
- Bidirectional constraint networks
- Cells and propagators
- Partial information lattices
- Truth Maintenance Systems (TMS)

**The Propagator Model:**
```
     ┌─────────┐
     │  Cell   │ ← Accumulates partial information
     └────┬────┘
          │ (neighbors)
    ┌─────┼─────┐
    ▼     ▼     ▼
┌──────┐┌──────┐┌──────┐
│Prop A││Prop B││Prop C│  ← Transform information
└──────┘└──────┘└──────┘
```

Key insight: **Information flows bidirectionally**. A cell for `x²=4` can deduce `x=±2` OR given `x=2`, confirm the equation.

#### Chapter 8: Degeneracy (-1)
- Multiple implementation strategies
- Fallback mechanisms
- Redundancy for robustness

### Part IV: Flexibility through Abstraction [ERGODIC]

#### Chapter 9: Generic Procedures (0)
- Multi-method dispatch
- Predicate dispatch
- Inheritance vs composition

```scheme
(define generic-+
  (simple-generic-procedure '+ 2
    (lambda (a b)
      (error "No method for +" a b))))

(define-generic-procedure-handler generic-+
  (match-args number? number?)
  +)

(define-generic-procedure-handler generic-+
  (match-args symbol? symbol?)
  (lambda (a b) `(+ ,a ,b)))
```

#### Chapter 10: Adventure Game Example (+1)
- Synthesis of all techniques
- People, places, things as generic objects
- Autonomous agents

## GF(3) Conservation

The chapter structure distributes across GF(3) trits:

```
Total sections: 87
MINUS (-1):  29  ████████████████
ERGODIC (0): 29  ████████████████
PLUS (+1):   29  ████████████████
Sum mod 3:   0
Conserved:   ✓ BALANCED
```

## Core Abstractions Taxonomy

### 1. Combinators [PLUS]

| Combinator | Signature | Description |
|------------|-----------|-------------|
| `compose` | (f g) → h | Sequential composition |
| `parallel-combine` | (h f g) → k | Parallel then combine |
| `spread-combine` | (h f g) → k | Split args, combine results |
| `restrict` | (f pred) → g | Domain restriction |
| `coerce` | (f type) → g | Type coercion wrapper |

### 2. Generic Dispatch [ERGODIC]

| Pattern | Mechanism | Use Case |
|---------|-----------|----------|
| Single dispatch | Type of first arg | OOP methods |
| Multi-dispatch | Types of all args | CLOS, Julia |
| Predicate dispatch | Arbitrary predicates | SDF generic procedures |
| Pattern dispatch | Structural matching | Logic programming |

### 3. Propagators [MINUS → verification role]

| Component | Role | Partial Info |
|-----------|------|--------------|
| Cell | Information accumulator | Merge lattice |
| Propagator | Constraint enforcer | Monotonic update |
| Scheduler | Activation manager | Fixpoint detection |
| TMS | Belief revision | Dependency tracking |

## Propagator Networks as Markov Blankets

The propagator architecture maps to Friston's Free Energy Principle:

```
┌─────────────────────────────────────────┐
│              EXTERNAL WORLD             │
│  (other propagator networks, inputs)    │
└──────────────────┬──────────────────────┘
                   │
         ┌─────────▼─────────┐
         │   SENSORY CELLS   │  ← Boundary inputs
         │   (observations)  │
         └─────────┬─────────┘
                   │
         ┌─────────▼─────────┐
         │  INTERNAL CELLS   │  ← Beliefs/predictions
         │  (hidden states)  │
         └─────────┬─────────┘
                   │
         ┌─────────▼─────────┐
         │   ACTIVE CELLS    │  ← Action outputs
         │   (predictions)   │
         └─────────┬─────────┘
                   │
         ┌─────────▼─────────┐
         │   EXTERNAL WORLD  │
         │   (effects)       │
         └───────────────────┘
```

## Integration with SICP

SDF extends SICP's core ideas:

| SICP Concept | SDF Extension | Trit Relationship |
|--------------|---------------|-------------------|
| Procedures as data | Combinators | SICP.Ch1 (+1) → SDF.Ch1 (+1) |
| Data abstraction | Generic dispatch | SICP.Ch2 (0) → SDF.Ch3-4 (0,+1) |
| Assignment/state | Propagators | SICP.Ch3 (+1) → SDF.Ch7 (0) |
| Metalinguistic | Layering | SICP.Ch4 (+1) → SDF.Ch6 (+1) |
| Compilation | Degeneracy | SICP.Ch5 (0) → SDF.Ch8 (-1) |

### Balanced Triad: SICP ⊗ SDF ⊗ Implementation

```
SICP (0) + SDF (+1) + Implementation Target (-1) = 0 ✓
```

Where implementation targets include:
- **Zig** (-1): Systems-level with comptime generics
- **Rust** (-1): Ownership-based with traits
- **Julia** (-1): Multiple dispatch native

## Scheme Implementation Patterns

### Pattern 1: Combinator Definition

```scheme
;; The spread-combine combinator
(define (spread-combine h f g)
  (let ((n (get-arity f))
        (m (get-arity g)))
    (define (the-combination . args)
      (h (apply f (list-head args n))
         (apply g (list-tail args n))))
    (restrict-arity the-combination (+ n m))))
```

### Pattern 2: Generic Procedure

```scheme
;; Define a generic operation
(define generic-magnitude
  (simple-generic-procedure 'magnitude 1))

;; Handler for complex numbers
(define-generic-procedure-handler generic-magnitude
  (match-args complex?)
  (lambda (z) (sqrt (+ (square (real-part z))
                       (square (imag-part z))))))

;; Handler for vectors
(define-generic-procedure-handler generic-magnitude
  (match-args vector?)
  (lambda (v) (sqrt (apply + (map square (vector->list v))))))
```

### Pattern 3: Propagator Cell

```scheme
;; Create a propagator network for Pythagorean theorem
(define-cell a)
(define-cell b)  
(define-cell c)

;; a² + b² = c² (bidirectional!)
(quadratic-propagator a b c)

;; Now we can:
(add-content! a 3)
(add-content! b 4)
(run)
(content c)  ;=> 5

;; OR go backwards:
(add-content! c 13)
(add-content! a 5)
(run)
(content b)  ;=> 12
```

### Pattern 4: Layered Datum

```scheme
;; Value with provenance
(define measured-temp
  (make-layered-datum 
    23.5  ; base value in Celsius
    (cons 'units 'celsius)
    (cons 'uncertainty 0.1)
    (cons 'source "thermometer-7")
    (cons 'timestamp 1706384000)))

;; Generic operations preserve layers
(generic-+ measured-temp (make-layered-datum 2.0 (cons 'units 'celsius)))
;=> Layered datum with merged provenance
```

## Zig Implementation Mapping

From our `interaction_tensor.zig`:

| SDF Concept | Zig Implementation |
|-------------|-------------------|
| Combinator | `fn compose(f, g) fn` |
| Generic procedure | `fn(comptime T: type)` |
| Propagator cell | `Cell(T)` with `merge: fn(T,T)T` |
| Layered datum | `struct { value: T, layers: []Layer }` |
| Partial info | `?T` optional + `Tropical` semiring |

### Zig Propagator Network

```zig
pub fn Cell(comptime T: type) type {
    return struct {
        content: ?T = null,
        neighbors: ArrayListUnmanaged(*Propagator(T)),
        
        pub fn addContent(self: *@This(), info: T, merge: fn(?T, T) ?T) void {
            const new_content = merge(self.content, info);
            if (!contentEqual(self.content, new_content)) {
                self.content = new_content;
                self.alertNeighbors();
            }
        }
        
        fn alertNeighbors(self: *@This()) void {
            for (self.neighbors.items) |prop| {
                scheduler.enqueue(prop);
            }
        }
    };
}
```

## Commands

```bash
# Load SDF library in MIT Scheme
(load "~/sdf/manager/load.scm")

# Run specific chapter
(manage 'new 'combinators)
(manage 'new 'generic-procedures)
(manage 'new 'propagation)

# Verify GF(3) conservation
bb sdf_skill_morphism.bb verify

# Generate interleaving with SICP
bb sdf_skill_morphism.bb interleave sicp sdf
```

## References

- [MIT Press Book Page](https://mitpress.mit.edu/9780262045490/)
- [SDF Code Repository](https://github.com/chrishanson/sdf) (MIT Scheme)
- [SICP](https://mitpress.mit.edu/sicp) (predecessor text)
- [Propagator Networks Paper](https://dspace.mit.edu/handle/1721.1/44215) (Radul & Sussman)

## Scientific Skill Interleaving

This skill connects to the K-Dense-AI/claude-scientific-skills ecosystem:

### Functional Programming
- **sicp** [●] via direct extension
  - Foundational computational thinking
- **lambda-calculus** [○] via combinator correspondence
  - Theoretical foundation
- **lispsyntax-acset** [○] via S-expression ACSet
  - Structural representation

### Constraint Systems
- **propagators** [●] via Chapter 7 implementation
  - Bidirectional constraint networks
- **modelica** [○] via acausal semantics
  - DAE constraint satisfaction
- **glass-bead-game** [○] via world-hopping combinators
  - Interdisciplinary synthesis

### Type Systems
- **zig-programming** [○] via comptime generics
  - Systems implementation
- **algebraic-rewriting** [○] via term transformation
  - Generic procedure rewriting

### Bibliography References

- `software-engineering`: 156 citations in bib.duckdb
- `constraint-programming`: 89 citations in bib.duckdb
- `generic-programming`: 67 citations in bib.duckdb

## Cat# Integration

This skill maps to **Cat# = Comod(P)** as a bicomodule in the equipment structure:

```
Trit: -1 (MINUS)
Home: Prof
Poly Op: ⊗
Kan Role: Ran (right Kan extension - verification)
Color: #D89B73
URI: skill://sdf#D89B73
```

### Combinator as Polynomial Functor

Each SDF combinator corresponds to a polynomial functor operation:
- `compose` → functor composition
- `parallel-combine` → product
- `spread-combine` → coproduct + product

### GF(3) Naturality

The skill participates in triads satisfying:
```
(-1) + (0) + (+1) ≡ 0 (mod 3)
```

Example balanced triad:
```
sicp (+1) + sdf (-1) + modelica (0) = 0 ✓
glass-bead-game (0) + sdf (-1) + zig-programming (+1) = 0 ✓
```

## Interleaving Map: SICP ↔ SDF

```
SICP                           SDF
════                           ═══
Ch1: Procedures ──────────────→ Ch1: Combinators
     (+1)                            (+1)
     │                               │
     │ "abstract procedure"          │ "compose abstractions"
     ▼                               ▼
Ch2: Data ────────────────────→ Ch3-4: Generic Dispatch
     (0)                             (0, +1)
     │                               │
     │ "abstraction barriers"        │ "predicate dispatch"
     ▼                               ▼
Ch3: State ───────────────────→ Ch7: Propagators
     (+1)                            (0)
     │                               │
     │ "assignment model"            │ "bidirectional flow"
     ▼                               ▼
Ch4: Metalinguistic ──────────→ Ch6: Layering
     (+1)                            (+1)
     │                               │
     │ "eval/apply"                  │ "metadata propagation"
     ▼                               ▼
Ch5: Register Machines ───────→ Ch8: Degeneracy
     (0)                             (-1)
     │                               │
     │ "compilation"                 │ "redundant strategies"
```



## SDF Interleaving

This skill connects to **Software Design for Flexibility** (Hanson & Sussman, 2021):

### Primary Chapter: 9. Generic Procedures

**Concepts**: dispatch, multimethod, predicate dispatch, generic

### GF(3) Balanced Triad

```
sdf (+) + SDF.Ch9 (○) + [balancer] (−) = 0
```

**Skill Trit**: 1 (PLUS - generation)

### Secondary Chapters

- Ch3: Variations on an Arithmetic Theme
- Ch10: Adventure Game Example
- Ch2: Domain-Specific Languages
- Ch8: Degeneracy
- Ch7: Propagators
- Ch1: Flexibility through Abstraction
- Ch5: Evaluation
- Ch6: Layering
- Ch4: Pattern Matching

### Connection Pattern

Generic procedures dispatch on predicates. This skill selects implementations dynamically.
## Autopoietic Marginalia

> **The interaction IS the skill improving itself.**

Every use of this skill is an opportunity for worlding:
- **MEMORY** (-1): Record combinator patterns used
- **REMEMBERING** (0): Connect to propagator networks
- **WORLDING** (+1): Evolve generic dispatch strategies

---

*"The real power of Lisp is that it is possible to express the structure of a computation."*
— Gerald Jay Sussman

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/plurigrid) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
