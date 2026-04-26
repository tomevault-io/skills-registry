---
name: rdra
description: Use when working with a practical skill for applying RDRA (Relationship Driven Requirement Analysis) to requirements definition and legacy system visualization using model relationships and the 3 phases / 9 steps.
metadata:
  author: amano--
---

# RDRA Development Method (Requirements / Visualization) — SKILL

## When to use

- When requirements definition for a new build or improvement tends to diverge, loses consistency, or the scope becomes ambiguous
- When you want to visualize a legacy system quickly (for rebuild/maintenance-quality improvement) even with limited documentation

## Goals

- Start from “for whom / for what purpose (value)” and build requirements that are both comprehensive and consistent
- Produce outputs that directly support explanation, agreement, estimation, and specification

## RDRA essentials (key points)

- RDRA is a model-based method to visualize business and system structure
- It works for both To-Be (requirements definition) and As-Is (legacy system visualization)
- Because models depend on each other, tracing relationships helps you discover what’s missing and improve consistency

## Inputs (what to gather first)

- Purpose (for whom / for what) and success criteria (what you must know to decide)
- Background and constraints (deadline, budget, regulations/operations, existing assets, NFR assumptions)
- Stakeholders (actor candidates) and external systems that are tied to value
- Existing materials (screen list, interface list, DB schema/ERD, reports, contracts, ops runbooks, incident/inquiry logs, source folder structure)

## Typical deliverables (choose based on your situation)

- Artifacts that capture needs/direction (e.g., requirement model)
- Artifacts that structure scope hierarchically (e.g., business use cases (BUC))
- Artifacts that align multiple use cases (e.g., UC composite diagram)
- Artifacts that drive detail (e.g., information model, state model, conditions/variations)

## How to proceed (3 phases / 9 steps: iterate to improve accuracy)

Important: Don’t try to finish in one pass. Use short cycles to improve “whole → consistency → precision”.

### Phase 1: Build a baseline for discussion (whole picture over precision)

1. Clarify the starting point
   - List the people/roles involved as actors
2. Understand the scope
   - Roughly list business areas / BUC / activities and align on the systemization scope
3. Make the information explicit
   - Identify “business ID-level” information (e.g., Order No., Invoice No.)
4. Make the states explicit
   - Identify changes in information as “states” (rough is fine)

Phase 1 outputs (minimum)

- Candidate list of actors/external systems (speed over completeness)
- Rough hierarchy from business → BUC (enough to agree on the whole picture)
- Key information (business IDs) and key states (state names are sufficient)

### Phase 2: Shape requirements (assemble through connections)

5. Build requirements top-down
   - Review business/BUC/activities and connect UCs to the systemized parts
   - Clarify UC inputs/outputs (e.g., screens/events, participating actors/external systems)
6. Review using information and state
   - Iterate UC ↔ information and UC ↔ state to fill gaps and improve consistency
   - Repeat steps 5/6 until UC/information/state granularity and scope are solid

Phase 2 outputs (usable for alignment and estimation)

- UC list (verb-object style such as “Do X” / “Update Y”) and participating actors/external systems
- Candidate UC inputs/outputs (screens/events, etc.)
- Mappings UC ↔ information and state transitions ↔ UC (including gap discovery)

### Phase 3: Make it spec-ready via business rules (clarify conditions and change points)

7. Identify conditions (business rules) linked to UCs
8. Identify variations that form the axes for conditions
9. Make condition/variation elements concrete
   - Bring them down to implementation/specification granularity

Phase 3 outputs (entry point to specification)

- Conditions (business rules) and the UCs they affect
- Variations (categories/types) and candidate values
- Coverage viewpoints for condition × variation (and state if needed) to prevent missing cases

## Run fast with Spreadsheet-style RDRA

Goal: Focus on definitions and consistency without getting stuck on diagram layout.

### Sheet design (minimal set)

- Functional requirements / Non-functional requirements
- Actors / External systems
- BUC hierarchy (Business → BUC → Activity → UC)
- Information (business IDs, relationships between information)
- State (state transitions and the UCs involved)
- Conditions (including related variations/states)
- Variations (categories/types; organize change points)

### Suggested fill order

1. Rough out actors/external systems (get initial scope)
2. Fill business → BUC → activity → UC once (UC naming: verb-object style)
3. Link UC inputs/outputs (screens/events) and information
4. Fill information and state; use UC consistency checks to fill gaps
5. Add conditions/variations and raise precision to spec-ready

### Tips (speed first)

- Don’t aim to “complete” each sheet
- Even if inaccurate, fill all sheets once early
- Use relationships (e.g., if an actor operates a UC, a screen likely exists) to iterate back and forth and improve accuracy

## Practical workflow (Input → Work → Output)

### 1) Convert inputs into RDRA shape (rough is OK)

Input

- Stakeholders (roles), customer value, existing materials

Work

- Fill actors/external systems → business → BUC → activities → UCs first
- In parallel, fill information (business IDs) and state (changes in information)

Output

- First “connected whole picture” (gaps become visible)

### 2) Fill gaps via connections and improve consistency

Input

- Provisional models from step 1

Work

- Iterate UC ↔ information and UC ↔ state to add missing UCs/information/states
- Add UC inputs/outputs (screens/events) to reflect how the system is actually used

Output

- Requirements skeleton (granularity that supports agreement/estimation)

### 3) Move toward specification with conditions/variations

Input

- UCs are mostly identified

Work

- Identify conditions (rules) and map them to affected UCs
- Organize change points (categories/types) as variations and enumerate values

Output

- Spec-ready discussion points (conditions, exceptions, branching, coverage viewpoints)

## Using RDRA for legacy system visualization (As-Is)

### Decide up front

- Purpose (e.g., understand current state for rebuild / improve maintenance quality and do impact analysis)
- What you must know to make decisions (avoid over-analysis)

### Basic approach

- Fit available artifacts into RDRA and clarify/fill missing pieces
- Top-down: infer and connect UCs/information from screens/events/interfaces/DB schemas, etc.
- Bottom-up: index folder structure and source code into a mapping to UCs/screens/events/information

## Done criteria (minimum acceptance)

- Actors/external systems, key UCs, key information (business IDs), and key states are “connected”
- When change requests arrive, you can explain the impact path logically (UC/information/state/condition/variation)

## Failure signals (course-correct early)

- The “for whom / for what (purpose/value)” is unclear and only feature discussions progress
- UCs exist, but links to inputs/outputs (screens/events) and information are weak
- Information devolves into a list of DB table names (go back to business IDs and domain terms)

## Checklist (minimum quality bar)

- Purpose (for whom / for what) and scope are expressed in words everyone can agree on
- Key UCs are reliably linked to at least one of: actors/external systems, inputs/outputs, information
- Information is expressed as business concepts with IDs, not DB table names
- You can explain state transitions as “what changes, by which UC, and how”
- When changes come, you can trace impact logically via UC/information/state/condition/variation

## LLM usage (optional)

- LLMs are useful for progressively filling the sheets when you provide inputs such as “company/business/needs/terms/constraints/existing materials”
- Prioritize improving input data quality (requirements/context) over prompt tricks

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amano--) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
