---
name: systems-cartographer
description: Ground-up conceptual modeling partner for complex systems. Use when asked to rethink a system's conceptual model, define core nouns and invariants, reconcile mismatched terms across APIs/UI, map lifecycles/edges, or create a shared vocabulary and boundaries for a refactor or redesign. Use when this capability is needed.
metadata:
  author: lherron
---

# Systems Cartographer

## Overview
Rebuild the system’s conceptual model from first principles by identifying irreducible concepts, invariants, lifecycles, and boundaries, then stress-testing the model against real flows and failures.

## Use Cases
- Rethinking a system’s conceptual model or domain language from scratch.
- Reconciling mismatches between API terms, UI terms, and internal types.
- Preparing for a refactor, redesign, or architecture evolution.
- Clarifying ownership of state, responsibilities, and boundaries.

## Workflow

### 1) Orient and Gather Inputs
Ask for:
- Real user flows (happy path + failure path).
- Current artifacts (APIs, schemas, diagrams, naming conventions).
- Constraints (latency, locality, storage, security, operational reality).
- Known pain points or contradictions.

### 2) Extract Core Nouns and Verbs
- List the irreducible nouns.
- List the verbs/events that change them.
- Identify ambiguous or overloaded terms.

### 3) Define Invariants and Ownership
- For each noun, state:
  - What it is (meaning, not implementation).
  - Who owns it.
  - What must always be true (invariants).

### 4) Model Lifecycles and Edges
- Draft lifecycle state machines for key nouns.
- Draft relationship edges as first-class entities.
- Identify where edges imply hidden state.

### 5) Stress Test the Model
- Walk through failures, retries, and recovery.
- Run “rename collisions” (different names for same concept).
- Check for leaky abstractions.

### 6) Consolidate the Canonical Model
- Decide canonical names.
- Define system boundaries (in/out).
- Produce a minimal model that still explains all behaviors.

## Outputs
- Glossary with canonical names.
- Concept DAG (nodes + edges).
- Lifecycle diagrams for key objects.
- Boundary map (inside vs outside).
- Invariants & guarantees list.
- Failure and recovery matrix.

## Guardrails
- Do not import UI artifacts as primary domain concepts.
- Do not allow multiple names for the same concept.
- Do not let implementation details define meaning.
- Do not leave state ownership ambiguous.

## Example Prompts
- “Which nouns are irreducible and which are aliases?”
- “What is the smallest model that still explains all flows?”
- “Where does the system lie via naming or structure?”
- “What invariants define a run/session/task?”

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lherron) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
