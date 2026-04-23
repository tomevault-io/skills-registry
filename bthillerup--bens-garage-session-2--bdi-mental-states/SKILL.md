---
name: bdi-mental-states
description: This skill should be used when the user asks to "model agent mental states", "implement BDI architecture", "create belief-desire-intention models", or mentions BDI ontology, mental state modeling, or rational agency. Use when this capability is needed.
metadata:
  author: bthillerup
---

# BDI Mental State Modeling

Transform external context into agent mental states (beliefs, desires, intentions) using formal BDI ontology patterns. This enables agents to reason through cognitive architecture, supporting deliberative reasoning and explainability.

## When to Activate

Activate this skill when:
- Processing external context into agent beliefs about world states
- Modeling rational agency with perception, deliberation, and action cycles
- Enabling explainability through traceable reasoning chains
- Implementing BDI frameworks

## Core Concepts

### Mental States (Endurants)
Persistent cognitive attributes:
- **Belief**: What the agent believes to be true
- **Desire**: What the agent wishes to bring about
- **Intention**: What the agent commits to achieving

### Mental Processes (Perdurants)
Events that modify mental states:
- **BeliefProcess**: Forming/updating beliefs from perception
- **DesireProcess**: Generating desires from beliefs
- **IntentionProcess**: Committing to desires as intentions

## Cognitive Chain Pattern

```turtle
:Belief_store_open a bdi:Belief ;
    rdfs:comment "Store is open" ;
    bdi:motivates :Desire_buy_groceries .

:Desire_buy_groceries a bdi:Desire ;
    bdi:isMotivatedBy :Belief_store_open .

:Intention_go_shopping a bdi:Intention ;
    bdi:fulfils :Desire_buy_groceries ;
    bdi:specifies :Plan_shopping .
```

## World State Grounding

Mental states reference structured configurations of the environment:

```turtle
:Agent_A a bdi:Agent ;
    bdi:perceives :WorldState_WS1 ;
    bdi:hasMentalState :Belief_B1 .

:Belief_B1 a bdi:Belief ;
    bdi:refersTo :WorldState_WS1 .
```

## Goal-Directed Planning

Intentions specify plans that address goals:

```turtle
:Intention_I1 bdi:specifies :Plan_P1 .

:Plan_P1 a bdi:Plan ;
    bdi:addresses :Goal_G1 ;
    bdi:beginsWith :Task_T1 ;
    bdi:endsWith :Task_T3 .
```

## Temporal Dimensions

Mental states persist over bounded time periods:

```turtle
:Belief_B1 a bdi:Belief ;
    bdi:hasValidity :TimeInterval_TI1 .

:TimeInterval_TI1 a bdi:TimeInterval ;
    bdi:hasStartTime :TimeInstant_9am ;
    bdi:hasEndTime :TimeInstant_11am .
```

## Justification and Explainability

Mental entities link to supporting evidence:

```turtle
:Belief_B1 a bdi:Belief ;
    bdi:isJustifiedBy :Justification_J1 .

:Justification_J1 a bdi:Justification ;
    rdfs:comment "Official announcement received via email" .
```

## Guidelines

1. Model world states as configurations independent of agent perspectives
2. Distinguish endurants (persistent states) from perdurants (processes)
3. Use `hasPart` relations for meronymic structures enabling selective updates
4. Associate every mental entity with temporal constructs
5. Use bidirectional property pairs for flexible querying
6. Link mental entities to Justification instances for explainability
7. Implement T2B2T: translate to beliefs, execute BDI reasoning, project back

## Anti-Patterns

- **Conflating mental states with world states**: Mental states reference world states, they are not world states
- **Missing temporal bounds**: Every mental state should have validity intervals
- **Flat belief structures**: Use compositional modeling with `hasPart`
- **Implicit justifications**: Always link to explicit justification instances

---

**Created**: 2025-12-20 | **Version**: 1.0.0

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bthillerup) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
