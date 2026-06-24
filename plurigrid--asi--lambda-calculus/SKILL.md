---
name: lambda-calculus
description: Lambda Calculus Skill Use when this capability is needed.
metadata:
  author: plurigrid
---

# lambda-calculus Skill


> *"Three rules. Infinite computation. The foundation of all functional programming."*

## Overview

**Lambda Calculus** implements Church's lambda calculus, the mathematical foundation of functional programming. Variables, abstraction, and application - that's all you need.

## GF(3) Role

| Aspect | Value |
|--------|-------|
| Trit | +1 (PLUS) |
| Role | GENERATOR |
| Function | Generates terms and reductions |

## The Three Rules

```
┌─────────────────────────────────────────────────────────────────┐
│                    LAMBDA CALCULUS SYNTAX                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Term ::= x           Variable                                  │
│        |  λx. Term    Abstraction (function definition)        │
│        |  Term Term   Application (function call)               │
│                                                                 │
│  That's it. Everything else is encoded.                        │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## β-Reduction

```
The only computation rule:

(λx. M) N  →β  M[x := N]

"Apply function λx.M to argument N by substituting N for x in M"

Example:
(λx. x x) (λy. y)
→β (λy. y) (λy. y)
→β λy. y

```

## Church Encodings

```haskell
-- Booleans
true  = λt. λf. t
false = λt. λf. f
if    = λb. λt. λf. b t f

-- Numbers (Church numerals)
zero  = λf. λx. x
one   = λf. λx. f x
two   = λf. λx. f (f x)
three = λf. λx. f (f (f x))

succ  = λn. λf. λx. f (n f x)
plus  = λm. λn. λf. λx. m f (n f x)
mult  = λm. λn. λf. m (n f)

-- Pairs
pair  = λx. λy. λf. f x y
fst   = λp. p (λx. λy. x)
snd   = λp. p (λx. λy. y)

-- Lists
nil   = λc. λn. n
cons  = λh. λt. λc. λn. c h (t c n)
```

## Fixed Point Combinator

```haskell
-- Y combinator: enables recursion without recursion!
Y = λf. (λx. f (x x)) (λx. f (x x))

-- Y F = F (Y F)
-- This gives us recursion in a language without built-in recursion

-- Example: factorial
fact = Y (λf. λn. if (isZero n) one (mult n (f (pred n))))
```

## Reduction Strategies

```python
class LambdaReducer:
    """Different reduction strategies for lambda calculus."""

    TRIT = 1  # GENERATOR role

    def beta_reduce(self, term: Term, strategy: str) -> Term:
        """
        Reduce term using specified strategy.
        """
        if strategy == 'normal':
            return self.normal_order(term)
        elif strategy == 'applicative':
            return self.applicative_order(term)
        elif strategy == 'lazy':
            return self.call_by_need(term)
        elif strategy == 'parallel':
            return self.parallel_reduce(term)

    def normal_order(self, term: Term) -> Term:
        """
        Leftmost-outermost reduction.
        Always finds normal form if it exists.
        """
        while True:
            redex = self.find_leftmost_outermost(term)
            if redex is None:
                return term  # Normal form
            term = self.reduce_at(term, redex)

    def applicative_order(self, term: Term) -> Term:
        """
        Leftmost-innermost reduction.
        May not terminate even if normal form exists.
        """
        while True:
            redex = self.find_leftmost_innermost(term)
            if redex is None:
                return term
            term = self.reduce_at(term, redex)

    def call_by_need(self, term: Term) -> Term:
        """
        Lazy evaluation with sharing.
        Optimal in many cases.
        """
        return self.lazy_reduce_with_sharing(term)
```

## De Bruijn Indices

```
Named:               De Bruijn:
λx. λy. x y    →    λ. λ. 2 1
λx. λy. y x    →    λ. λ. 1 2
λx. x          →    λ. 1

Index n refers to the variable bound by the nth enclosing λ
No more α-equivalence problems!
```

## Types (Simply Typed Lambda Calculus)

```
Types:  τ ::= α | τ → τ

Typing rules:

Γ, x:τ ⊢ x : τ                          (Var)

Γ, x:σ ⊢ M : τ
─────────────────                       (Abs)
Γ ⊢ λx.M : σ → τ

Γ ⊢ M : σ → τ    Γ ⊢ N : σ
────────────────────────────            (App)
      Γ ⊢ M N : τ
```

## GF(3) Term Classification

```python
class GF3Lambda:
    """Classify lambda terms by GF(3) role."""

    def classify(self, term: Term) -> int:
        """
        GENERATOR (+1): Abstractions (create functions)
        COORDINATOR (0): Applications (route computation)
        VALIDATOR (-1): Variables (consume bindings)
        """
        match term:
            case Var(_):
                return -1  # Consumes a binding
            case Lam(_, body):
                return 1   # Creates a function
            case App(func, arg):
                return 0   # Routes computation

    def verify_conservation(self, term: Term) -> bool:
        """Check GF(3) conservation in term structure."""
        def sum_trits(t):
            match t:
                case Var(_):
                    return -1
                case Lam(_, body):
                    return 1 + sum_trits(body)
                case App(func, arg):
                    return 0 + sum_trits(func) + sum_trits(arg)

        return sum_trits(term) % 3 == 0
```

## Interaction Net Compilation

```
Lambda term:        Interaction net:

λx. x              ┌───┐
                   │ λ │
                   └─┬─┘
                     │
                   ┌─┴─┐
                   │ x │
                   └───┘

(λx.x) y           ┌───┐     ┌───┐
                   │ @ │─────│ y │
                   └─┬─┘     └───┘
                     │
                   ┌─┴─┐
                   │ λ │
                   └─┬─┘
                     │
                   ┌─┴─┐
                   │ x │
                   └───┘
```

## GF(3) Triads

```
lambda-calculus (+1) ⊗ interaction-nets (0) ⊗ linear-logic (-1) = 0 ✓
lambda-calculus (+1) ⊗ datalog-fixpoint (0) ⊗ type-checker (-1) = 0 ✓
lambda-calculus (+1) ⊗ hyjax-relational (0) ⊗ narya-proofs (-1) = 0 ✓
```

## Commands

```bash
# Parse and reduce lambda term
just lambda-reduce "(\x. x x) (\y. y)"

# Show reduction steps
just lambda-trace "(λx. λy. x) a b" --strategy normal

# Convert to de Bruijn
just lambda-debruijn "λx. λy. x y"

# Type infer
just lambda-type "λx. λy. x"

# Compile to interaction net
just lambda-to-inet "λf. λx. f (f x)"
```

---

**Skill Name**: lambda-calculus
**Type**: Computation Theory / Foundations
**Trit**: +1 (PLUS - GENERATOR)
**GF(3)**: Generates terms and reductions

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
