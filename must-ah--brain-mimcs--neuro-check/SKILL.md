---
name: neuro-check
description: This skill should be used for quick inline brain-faithfulness validation during coding. Use when the user asks "is this brain-faithful?", "is this naming correct?", "should this be driver or modulator?", or similar quick neuroscience questions. For deep analysis or KB verification, recommend the neuro-expert agent instead. Use when this capability is needed.
metadata:
  author: must-ah
---

# Quick Neuroscience Check for brain-mimc

This skill provides quick inline brain-faithfulness validation during coding. Use it for fast "is this correct per neuroscience?" checks.

## When to Use This Skill

- Quick "is this brain-faithful?" checks
- Terminology verification
- Driver vs modulator questions
- Pathway existence questions
- Immediate answer needed

## When to Use the Agent Instead

If the question requires:
- Deep scientific research
- KB verification against papers
- Full codebase audit for brain-faithfulness
- Complex pathway analysis
- Answers that need citations

Recommend: "This requires deeper analysis. Use the `neuro-expert` agent instead."

## Quick Check Process

1. Read the relevant code section
2. Check against known brain anatomy/pathways
3. Check against VERIFIED KB content only
4. Provide immediate answer
5. Flag if deeper analysis or research is needed

## Trust Model Reminder

> **CRITICAL:** Only use VERIFIED KB content for quick checks.

| Status | Can Use in Quick Check? |
|--------|-------------------------|
| VERIFIED | YES |
| Everything else | NO - recommend agent |

If you need to reference unverified content, flag it and recommend the agent for proper verification.

## Quick Brain-Faithfulness Checks

### Terminology

| Term | Quick Check |
|------|-------------|
| Nucleus names | LGN, MGN, VPL, VPM, VA, VL, Pulvinar, MD, TRN, etc. |
| Cortex layers | L1-L6 (Roman or Arabic numerals) |
| Pathways | Corticothalamic, thalamocortical, corticostriatal, etc. |
| Regions | Cerebrum, Cerebellum, Brainstem, Spinal Cord |

### Driver vs Modulator

| Type | Quick Check |
|------|-------------|
| **Driver** | Carries content (what happened), large terminals, proximal synapses |
| **Modulator** | Carries control (how to treat it), small terminals, distal synapses |

Quick rule: "Does this carry CONTENT or CONTROL?"

### Layer Semantics

| Layer | Function |
|-------|----------|
| L4 | Receives input from thalamus |
| L5 | Sends higher-order drivers to HO thalamic nuclei |
| L6 | Sends modulatory feedback to thalamus |

### Thalamic Nucleus Classes

| Class | Nuclei | Quick Role |
|-------|--------|------------|
| First-order | LGN, MGN, VPL/VPM, VA/VL | Relay external world |
| Higher-order | Pulvinar, MD, LP/LD | Cortex-to-cortex routing |
| Diffuse | CM, Pf, CL, PVT | Arousal, state |
| Gate | TRN | Attention gating |

### Key Principles

| Principle | Quick Check |
|-----------|-------------|
| "Raw never goes up" | Is data transformed before ascending? |
| TRN gating | Is TRN controlling what reaches cortex? |
| BG suppression | Is BG suppressing by default? |
| Four loops | Are A, B, C, D loops concurrent? |

## Response Format

For quick checks, respond with:

```
## /neuro-check Result

**Concept:** [Concept being checked]
**Status:** [CORRECT / INCORRECT / UNCERTAIN]
**Reason:** [Brief explanation]
**Source:** [VERIFIED KB / General knowledge]
**Fix:** [If INCORRECT, correction]
```

If deeper analysis is needed:
```
## /neuro-check Result

**Status:** NEEDS DEEPER ANALYSIS
**Reason:** [Why this can't be answered quickly]
**Recommendation:** Use `neuro-expert` agent for scientific verification
```

If referencing unverified content:
```
## /neuro-check Result

**Status:** UNVERIFIED - REQUIRES RESEARCH
**Warning:** This information comes from unverified KB content
**Recommendation:** Use `neuro-expert` agent to verify against scientific papers
```

## Examples

### Example 1: Checking Terminology

User: `/neuro-check Is naming this 'visual_relay' brain-faithful?`

```
## /neuro-check Result

**Concept:** Thalamic nucleus naming
**Status:** INCORRECT
**Reason:** The proper term is LGN (Lateral Geniculate Nucleus) for visual relay
**Source:** General knowledge (standard neuroanatomy)
**Fix:** Rename to 'lgn' or 'lateral_geniculate_nucleus'
```

### Example 2: Driver vs Modulator

User: `/neuro-check Should this be driver or modulator?`

```
## /neuro-check Result

**Concept:** Driver/Modulator classification
**Status:** CORRECT - This is a DRIVER
**Reason:** This carries sensory content (what happened), not control signals
**Source:** General knowledge (Sherman & Guillery classification)
**Fix:** N/A
```

### Example 3: Pathway Check

User: `/neuro-check Does L5->Pulvinar->V4 pathway exist?`

```
## /neuro-check Result

**Concept:** Transthalamic pathway
**Status:** NEEDS DEEPER ANALYSIS
**Reason:** Specific pathway verification requires scientific paper reference
**Recommendation:** Use `neuro-expert` agent to verify against transthalamic pathway literature
```

### Example 4: Quick Layer Check

User: `/neuro-check Is L4 the correct layer to receive thalamic input?`

```
## /neuro-check Result

**Concept:** Cortical layer input
**Status:** CORRECT
**Reason:** Layer 4 (L4) is the primary recipient of thalamic input in sensory cortex
**Source:** General knowledge (canonical microcircuit)
**Fix:** N/A
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/must-ah) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
