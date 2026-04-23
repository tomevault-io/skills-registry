---
name: categorical-structure-builder
description: Universal template for implementing categorical structures in meta-prompting frameworks. Applies to functors, monads, comonads, natural transformations, adjunctions, hom-equivalences, limits, colimits, and enriched categories. Use when extending frameworks with new categorical constructs. Use when this capability is needed.
metadata:
  author: manutej
---

# Categorical Structure Builder

A universal template for implementing any categorical structure as a command or skill in a meta-prompting framework.

## Purpose

This skill provides a **generalized template** that can instantiate:
- Functors (F: C → D)
- Monads (M: C → C with unit, bind, join)
- Comonads (W: C → C with extract, duplicate, extend)
- Natural Transformations (α: F ⇒ G)
- Adjunctions (F ⊣ G)
- Hom-Equivalences (Hom(F(A), B) ≅ Hom(A, G(B)))
- Limits and Colimits
- Enriched Categories ([0,1], Ab, Cat)

---

## Universal Structure Template

### Core Pattern

Every categorical structure implementation follows this pattern:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│ CATEGORICAL STRUCTURE: {structure_name}                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│ FORMAL DEFINITION:                                                           │
│   {formal_type_signature}                                                   │
│                                                                              │
│ OPERATIONS:                                                                  │
│   {operation_1}: {type_signature_1}  ({description_1})                     │
│   {operation_2}: {type_signature_2}  ({description_2})                     │
│   ...                                                                        │
│                                                                              │
│ LAWS:                                                                        │
│   1. {law_name_1}: {law_statement_1}                                       │
│   2. {law_name_2}: {law_statement_2}                                       │
│   ...                                                                        │
│                                                                              │
│ COMMAND SYNTAX:                                                              │
│   /{command} @mode:{operation} @{param}:{value} "task"                     │
│                                                                              │
│ COMPOSITION INTEGRATION:                                                     │
│   With →: {sequential_behavior}                                             │
│   With ||: {parallel_behavior}                                              │
│   With ⊗: {tensor_behavior}                                                 │
│   With >=>: {kleisli_behavior}                                              │
│                                                                              │
│ QUALITY PROPAGATION:                                                         │
│   {quality_rule}                                                            │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Instantiation Templates

### Template 1: Functor

```yaml
CATEGORICAL STRUCTURE: Functor {name}

FORMAL DEFINITION:
  F: C → D
  - F_obj: Obj(C) → Obj(D)           # Object mapping
  - F_mor: Mor(C) → Mor(D)           # Morphism mapping

OPERATIONS:
  map: (A → B) → F(A) → F(B)         # Lift function to functor context

LAWS:
  1. Identity:    F(id_A) = id_{F(A)}
  2. Composition: F(g ∘ f) = F(g) ∘ F(f)

COMMAND SYNTAX:
  /{name} @input:{source_type} @output:{target_type} "task"

COMPOSITION INTEGRATION:
  With →: F(A → B) composes as F(A) → F(B)
  With ||: F(A) || F(B) = F(A × B) via product preservation
  With ⊗: F(A) ⊗ F(B) follows monoidal functor rules
  With >=>: Lifts to Kleisli category if monad exists

QUALITY PROPAGATION:
  quality(F(A)) = quality(A)  # Functors preserve quality
```

**Example Instantiation - Route Functor:**
```yaml
CATEGORICAL STRUCTURE: Functor Route

FORMAL DEFINITION:
  F_route: Task → Prompt

OPERATIONS:
  map: (Task → Task') → (Prompt → Prompt')

LAWS:
  1. Identity: route(trivial_task) = trivial_prompt
  2. Composition: route(task_B ∘ task_A) = route(task_B) ∘ route(task_A)

COMMAND SYNTAX:
  /route "task description"
```

---

### Template 2: Monad

```yaml
CATEGORICAL STRUCTURE: Monad {name}

FORMAL DEFINITION:
  M: C → C  (endofunctor with extra structure)
  - unit: A → M(A)                   # η: Id ⇒ M
  - bind: M(A) → (A → M(B)) → M(B)   # Kleisli extension
  - join: M(M(A)) → M(A)             # μ: M∘M ⇒ M

OPERATIONS:
  unit:   A → M(A)                   # Wrap value in monadic context
  bind:   M(A) × (A → M(B)) → M(B)   # Chain computations
  join:   M(M(A)) → M(A)             # Flatten nested monads
  fmap:   (A → B) → M(A) → M(B)      # Inherited from functor

LAWS:
  1. Left Identity:  unit(a) >>= f  =  f(a)
  2. Right Identity: m >>= unit     =  m
  3. Associativity:  (m >>= f) >>= g = m >>= (λx. f(x) >>= g)

  # Equivalently via join:
  1'. join ∘ unit      = id
  2'. join ∘ fmap unit = id
  3'. join ∘ join      = join ∘ fmap join

COMMAND SYNTAX:
  /{name} @mode:unit|bind|join @quality:{threshold} "task"

COMPOSITION INTEGRATION:
  With →:   M(A) → M(B) via bind
  With ||:  M(A) || M(B) = M(A × B) via applicative
  With ⊗:   M(A) ⊗ M(B) quality degrades
  With >=>: Native Kleisli composition

QUALITY PROPAGATION:
  quality(unit(a)) = quality(a)
  quality(m >>= f) = min(quality(m), quality(f(extract(m))))
  # Quality can improve with iterative refinement
```

**Example Instantiation - RMP Monad:**
```yaml
CATEGORICAL STRUCTURE: Monad RMP

FORMAL DEFINITION:
  M_RMP: Prompt → Prompt  (iterative refinement)

OPERATIONS:
  unit:  prompt → MonadPrompt(prompt, quality=initial)
  bind:  MonadPrompt × improve → MonadPrompt'
  join:  MonadPrompt(MonadPrompt(p)) → MonadPrompt(p)

COMMAND SYNTAX:
  /rmp @quality:0.85 @max_iterations:5 "task"
```

---

### Template 3: Comonad

```yaml
CATEGORICAL STRUCTURE: Comonad {name}

FORMAL DEFINITION:
  W: C → C  (endofunctor, dual to monad)
  - extract:   W(A) → A              # ε: W ⇒ Id
  - duplicate: W(A) → W(W(A))        # δ: W ⇒ W∘W
  - extend:    (W(A) → B) → W(A) → W(B)

OPERATIONS:
  extract:   W(A) → A                # Focus on current value
  duplicate: W(A) → W(W(A))          # Create meta-observation
  extend:    (W(A) → B) → W(A) → W(B) # Context-aware transformation
  fmap:      (A → B) → W(A) → W(B)   # Inherited from functor

LAWS:
  1. Left Identity:  extract ∘ duplicate = id
  2. Right Identity: fmap extract ∘ duplicate = id
  3. Associativity:  duplicate ∘ duplicate = fmap duplicate ∘ duplicate

  # Equivalently via extend:
  1'. extend extract = id
  2'. extract ∘ extend f = f
  3'. extend f ∘ extend g = extend (f ∘ extend g)

COMMAND SYNTAX:
  /{name} @mode:extract|duplicate|extend @focus:{target} "task"

COMPOSITION INTEGRATION:
  With →:   W(A) → W(B) via extend
  With ||:  W(A) || W(B) = W(A × B) via comonoidal
  With ⊗:   W(A) ⊗ W(B) shares context
  With >=>: Co-Kleisli composition

QUALITY PROPAGATION:
  quality(extract(w)) ≤ quality(w)  # Extraction may lose context
  quality(duplicate(w)) = quality(w)
  quality(extend(f)(w)) = quality(f(w))
```

**Example Instantiation - Context Comonad:**
```yaml
CATEGORICAL STRUCTURE: Comonad Context

FORMAL DEFINITION:
  W_ctx: History → Context

OPERATIONS:
  extract:   History → FocusedContext
  duplicate: History → History(History)  # Meta-observation
  extend:    (History → Summary) → History → History(Summary)

COMMAND SYNTAX:
  /context @mode:extract @focus:recent @depth:3 "task"
  /context @mode:duplicate "create meta-observation"
  /context @mode:extend @transform:summarize "apply to context"
```

---

### Template 4: Natural Transformation

```yaml
CATEGORICAL STRUCTURE: Natural Transformation {name}

FORMAL DEFINITION:
  α: F ⇒ G  (transformation between functors)
  - α_A: F(A) → G(A)  for each object A

OPERATIONS:
  transform: F(A) → G(A)             # Apply transformation at object

NATURALITY CONDITION:
  For all f: A → B:
    α_B ∘ F(f) = G(f) ∘ α_A

  Diagram:
    F(A) ──F(f)──▶ F(B)
      │              │
    α_A            α_B
      ▼              ▼
    G(A) ──G(f)──▶ G(B)

LAWS:
  1. Naturality: α_B ∘ F(f) = G(f) ∘ α_A
  2. Identity:   α ∘ id_F = α = id_G ∘ α

COMMAND SYNTAX:
  /{name} @from:{functor_F} @to:{functor_G} "task"

COMPOSITION INTEGRATION:
  With →:   α; β composes natural transformations
  With ||:  α || β = α × β component-wise
  With ⊗:   α ⊗ β via monoidal structure
  With >=>: Horizontal composition

QUALITY PROPAGATION:
  quality(α_A(x)) = min(quality(F(A)), quality(G(A))) × transform_factor
```

**Example Instantiation - Strategy Transform:**
```yaml
CATEGORICAL STRUCTURE: Natural Transformation StrategySwitch

FORMAL DEFINITION:
  α: F_ZeroShot ⇒ F_ChainOfThought

OPERATIONS:
  transform: ZeroShotPrompt → ChainOfThoughtPrompt

NATURALITY:
  Switching strategy then applying to task =
  Applying to task then switching strategy

COMMAND SYNTAX:
  /transform @from:zero-shot @to:chain-of-thought "task"
```

---

### Template 5: Adjunction

```yaml
CATEGORICAL STRUCTURE: Adjunction {name}

FORMAL DEFINITION:
  F ⊣ G  (F left adjoint to G)
  - F: C → D  (left adjoint / free)
  - G: D → C  (right adjoint / forgetful)
  - unit:   η: Id_C ⇒ G∘F
  - counit: ε: F∘G ⇒ Id_D

HOM-SET ISOMORPHISM:
  Hom_D(F(A), B) ≅ Hom_C(A, G(B))

  This is natural in both A and B.

OPERATIONS:
  left_adjoint:  A → F(A)            # Free construction
  right_adjoint: B → G(B)            # Forgetful
  unit:          A → G(F(A))         # η_A
  counit:        F(G(B)) → B         # ε_B
  transpose_left:  (F(A) → B) → (A → G(B))   # Hom isomorphism
  transpose_right: (A → G(B)) → (F(A) → B)   # Inverse

LAWS (Triangle Identities):
  1. (ε_F) ∘ (F_η) = id_F
     F(A) ──F(η_A)──▶ F(G(F(A))) ──ε_{F(A)}──▶ F(A)  =  id_{F(A)}

  2. (G_ε) ∘ (η_G) = id_G
     G(B) ──η_{G(B)}──▶ G(F(G(B))) ──G(ε_B)──▶ G(B)  =  id_{G(B)}

COMMAND SYNTAX:
  /{name} @mode:free|forget|unit|counit @source:{category} "task"

COMPOSITION INTEGRATION:
  With →:   Adjunctions compose: (F ⊣ G) ∘ (F' ⊣ G') = (F∘F' ⊣ G'∘G)
  With ||:  Product of adjunctions
  With ⊗:   Monoidal adjunction
  With >=>: Kleisli from induced monad G∘F

QUALITY PROPAGATION:
  quality(F(A)) may differ from quality(A) (free construction adds structure)
  quality(G(B)) ≤ quality(B) (forgetful loses structure)

DERIVED STRUCTURES:
  - Monad:   G∘F with unit=η, multiplication=G(ε_F)
  - Comonad: F∘G with counit=ε, comultiplication=F(η_G)
```

**Example Instantiation - Task-Prompt Adjunction:**
```yaml
CATEGORICAL STRUCTURE: Adjunction TaskPrompt

FORMAL DEFINITION:
  F_gen ⊣ G_extract
  - F_gen: Task → Prompt      (generate prompt from task)
  - G_extract: Prompt → Task  (extract task from prompt)

HOM-SET ISOMORPHISM:
  Hom(GeneratedPrompt, TargetPrompt) ≅ Hom(Task, ExtractedTask)

COMMAND SYNTAX:
  /adjoint @mode:free "generate prompt from task"
  /adjoint @mode:forget "extract task from prompt"
```

---

### Template 6: Hom-Equivalence

```yaml
CATEGORICAL STRUCTURE: Hom-Equivalence {name}

FORMAL DEFINITION:
  Natural isomorphism of hom-sets:
  Hom_D(F(A), B) ≅ Hom_C(A, G(B))

  This is the core of adjunctions, but can exist independently.

OPERATIONS:
  forward:  Hom(F(A), B) → Hom(A, G(B))   # φ
  backward: Hom(A, G(B)) → Hom(F(A), B)   # φ⁻¹

LAWS:
  1. Inverse:    φ⁻¹ ∘ φ = id, φ ∘ φ⁻¹ = id
  2. Naturality: Commutes with composition in both arguments

DIAGRAM:
  Hom(F(A), B) ──────φ──────▶ Hom(A, G(B))
       │                           │
       │ precompose F(f)           │ precompose f
       ▼                           ▼
  Hom(F(A'), B) ─────φ──────▶ Hom(A', G(B))

COMMAND SYNTAX:
  /{name} @direction:forward|backward @hom:{source_hom} "morphism"

COMPOSITION INTEGRATION:
  With →:   Chain hom-equivalences
  With ||:  Product of hom-sets
  With ⊗:   Internal hom in monoidal category
  With >=>: Kleisli hom

QUALITY PROPAGATION:
  Isomorphisms preserve quality: quality(φ(f)) = quality(f)
```

---

### Template 7: Enriched Category

```yaml
CATEGORICAL STRUCTURE: Enriched Category {name} over {V}

FORMAL DEFINITION:
  Category C enriched over monoidal category V
  - Objects: Obj(C)
  - Hom-objects: Hom(A,B) ∈ V  (not just sets!)
  - Composition: Hom(B,C) ⊗ Hom(A,B) → Hom(A,C)
  - Identity: I_V → Hom(A,A)

ENRICHMENT BASE V:
  [0,1]-enriched: Hom-objects are quality scores
  Ab-enriched:    Hom-objects are abelian groups
  Cat-enriched:   Hom-objects are categories (2-categories)

OPERATIONS:
  hom:       (A, B) → V                    # Return hom-object
  compose:   V ⊗ V → V                     # Composition in V
  id:        () → V                        # Identity in V

LAWS:
  1. Associativity: Composition is associative in V
  2. Identity:      id_A composes correctly

COMMAND SYNTAX:
  /{name} @enrich:{base_V} @hom:{A},{B} "compute enriched hom"

QUALITY PROPAGATION ([0,1]-enriched):
  Hom(A,B) ∈ [0,1] IS the quality
  compose(q1, q2) = q1 ⊗ q2 = min(q1, q2)  # Tensor in [0,1]
```

**Example Instantiation - Quality-Enriched Prompts:**
```yaml
CATEGORICAL STRUCTURE: Enriched Category QualityPrompts over [0,1]

FORMAL DEFINITION:
  Prompts enriched over ([0,1], min, 1)

OPERATIONS:
  hom(A, B) = quality(A → B) ∈ [0,1]
  compose(q1, q2) = min(q1, q2)
  id = 1.0

COMMAND SYNTAX:
  /quality @hom:prompt_A,prompt_B "assess transformation quality"
```

---

## Usage Protocol

### Step 1: Identify the Categorical Structure

```
Question: What structure am I implementing?

□ Functor      - Preserves structure between categories
□ Monad        - Computation with effects/context
□ Comonad      - Context-dependent computation
□ Nat. Trans.  - Transformation between functors
□ Adjunction   - Pair of functors with universal property
□ Hom-Equiv.   - Isomorphism of hom-sets
□ Enriched     - Hom-objects with extra structure
□ Limit        - Universal cone
□ Colimit      - Universal cocone
□ Other        - Use general template
```

### Step 2: Instantiate the Template

```
1. Fill in FORMAL DEFINITION with types
2. List all OPERATIONS with signatures
3. State LAWS precisely
4. Design COMMAND SYNTAX following unified pattern
5. Define COMPOSITION behavior with all operators
6. Specify QUALITY propagation rule
```

### Step 3: Verify Laws

```
For each law:
  1. Write property-based test
  2. Generate test cases
  3. Verify law holds
  4. Document verification
```

### Step 4: Integrate with Framework

```
1. Create command file in .claude/commands/
2. Update ORCHESTRATION-SPEC.md
3. Update meta-self skill
4. Add to CLAUDE.md command table
5. Create integration tests
```

---

## Quick Reference Card

```
┌─────────────────────────────────────────────────────────────────────────────┐
│               CATEGORICAL STRUCTURE QUICK REFERENCE                         │
├──────────────┬──────────────────┬───────────────────────────────────────────┤
│ Structure    │ Key Operations   │ Core Law                                  │
├──────────────┼──────────────────┼───────────────────────────────────────────┤
│ Functor      │ map              │ F(g∘f) = F(g)∘F(f)                        │
│ Monad        │ unit, bind, join │ (m >>= f) >>= g = m >>= (x. f(x) >>= g)   │
│ Comonad      │ extract, extend  │ extract ∘ duplicate = id                  │
│ Nat. Trans.  │ transform        │ α_B ∘ F(f) = G(f) ∘ α_A                   │
│ Adjunction   │ unit, counit     │ (ε_F)∘(F_η) = id_F                        │
│ Hom-Equiv.   │ forward, back    │ φ⁻¹ ∘ φ = id                              │
│ Enriched     │ hom, compose     │ Associativity in V                        │
└──────────────┴──────────────────┴───────────────────────────────────────────┘
```

---

## Example: Applying Template to New Structure

**Task**: Add Kan Extension to the framework

**Step 1**: Identify → This is a universal construction (limit in functor category)

**Step 2**: Instantiate

```yaml
CATEGORICAL STRUCTURE: Left Kan Extension

FORMAL DEFINITION:
  Lan_K(F): D → E
  Given K: C → D and F: C → E, find universal Lan_K(F): D → E

  Unit: F ⇒ Lan_K(F) ∘ K

OPERATIONS:
  extend: F → Lan_K(F)           # Compute extension
  unit:   F(A) → Lan_K(F)(K(A))  # Universal morphism

LAWS:
  Universal Property:
  For any G: D → E with α: F ⇒ G∘K,
  there exists unique β: Lan_K(F) ⇒ G such that
  β_K ∘ η = α

COMMAND SYNTAX:
  /kan @mode:left @along:K @extend:F "compute left Kan extension"
```

---

## Version

**Skill Version**: 1.0
**Framework Compatibility**: 2.0+
**Categorical Coverage**: Functor, Monad, Comonad, NT, Adjunction, Hom-Equiv, Enriched
**Extensible**: Yes - use template pattern for new structures

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/manutej) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
