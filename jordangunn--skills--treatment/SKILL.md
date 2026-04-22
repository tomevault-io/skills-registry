---
name: doctor-treatment
description: | Use when this capability is needed.
metadata:
  author: jordangunn
---

# doctor-treatment

Produce a **treatment note** combining diagnosis, confidence, and proposed options.

## Purpose

- Diagnosis is a **claim with uncertainty**
- Treatment is a **proposal**, not an action

This skill creates an **artifact**, not execution.

## Epistemic Stance

- State diagnosis as an estimate, not a fact
- Include confidence level and falsification criteria
- Present options, not mandates
- Acknowledge risks and blast radius

## You MUST

- Clearly state the primary diagnosis
- Include confidence and what could falsify it
- Present multiple treatment options (not just one)
- Discuss risks and blast radius for each option
- Keep the note consumable by planning skills

## You MUST NOT

- Execute changes
- Conflate treatment with implementation
- Assume correctness beyond stated confidence
- Omit "do nothing" as an option when appropriate

## Output

A completed **Treatment Note** using the template at `../.resources/assets/TREATMENT_NOTE.md`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jordangunn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
