---
name: inversion-thinking
description: Proactive failure analysis using the Saboteur Method. Use when designing critical systems, security features, or debugging root causes. Use when this capability is needed.
metadata:
  author: calvyntwh
---

# Inversion Thinking (The Saboteur Method)

> "Tell me where I'm going to die so I will never go there." - Charlie Munger

## When to Use
*   **Before** writing complex code (Architecture, Security, Critical features).
*   **During** debugging (to find root causes).
*   **When** a plan seems "too simple" or "optimistic".

## The Protocol: Become the Saboteur
Do not ask "Does this work?". Ask "How do I break this?".
Follow these 4 steps explicitly.

### 1. Define the Anti-Goal
Explicitly write down the worst-case outcome.
*   *Goal:* "Keep user data safe."
*   *Anti-Goal:* "Leak all user data to the public internet."

### 2. The Saboteur Simulation
Adopt the persona of a malicious actor or the embodiment of Entropy (Murphy's Law).
Ask: **"I want to achieve the Anti-Goal. What is the easiest way to do it?"**

### 3. Proof of Fragility
List concrete, specific pathways to failure. Use `references/failure_modes.md` for inspiration.
*   "I will send a 10GB file to crash the memory."
*   "I will send a request with `admin=true`."
*   "I will unplug the database cable mid-transaction."

### 4. Inversion (The Fix)
Design specific safeguards to block those pathways.
*   "Enforce 10MB limit (HTTP 413)."
*   "Validate permissions on server side."
*   "Use atomic transactions with rollback."

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/calvyntwh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
