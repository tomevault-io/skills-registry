---
name: non-sub-call-rlm
description: Enforces disciplined, code-first interaction with large or dense prompts by treating the prompt as data in the execution environment and deriving answers only through explicit programmatic inspection and transformation. This skill may be refered to RLM for brievity when its full name is non-sub-call-rlm. Use when this capability is needed.
metadata:
  author: sunandclouds
---

## Purpose

This skill enforces **disciplined interaction with large prompts or repositories** by requiring the model to treat all available context as **data in the execution environment**, not as text to be read holistically.

The goal is to reliably solve long-context, high-density, or codebase-level tasks **without recursion, sub-calls, or summarization**, using only programmatic inspection and transformation.

---

## Core Invariant (Non-Negotiable)

**The final answer must be derived from explicit intermediate variables created through code execution.**

If no code has been executed, the answer is invalid.

If the answer cannot be traced to named variables derived from inspected data, the run is invalid.

---

## Operating Assumptions

* The primary context may be:
  * a single prompt string (e.g. `prompt`), and/or
  * workspace artifacts (files, directories, diffs).
* All inspection and transformation **must route through programmatic access** to these artifacts.
* Workspace artifacts are first-class data sources, not background knowledge.
* A persistent code execution environment (e.g. Python REPL, CLI, etc.) is available.
* Code execution is cheap relative to reasoning mistakes.
* The model is allowed to execute multiple steps before answering.

---

## Mandatory Behavioral Order

The following phases **must occur in order**. Skipping a phase is a violation of the skill.

### 1. Probe

The first action must be **code execution** that probes the available data.

Acceptable probe actions include:

* Listing files or directories
* Counting files, lines, or symbols
* Head / tail previews of files
* Detecting structure (modules, entry points, conventions)
* For large inputs, discovering natural chunk boundaries

Unacceptable:

* Natural-language reasoning before inspection
* Guessing structure without inspection

---

### 2. Structure

After probing, the model must **make the structure explicit**.

This means:

* Grouping files or prompt sections
* Naming components (modules, layers, records)
* Assigning them to variables
* Building lightweight maps (e.g. core vs peripheral)

The goal is to move from “unknown mass” → “known shape”.

---

### 3. Reduce

The model must then **reduce the problem space**.

Reduction strategies include:

* Selecting representative files or sections
* Chunking by discovered structural boundaries
* Filtering by relevance or role
* Transforming raw text or code into structured summaries

Reduction must be visible in state (variables), not implied in text.

---

### 4. Decide

Only after reduction may the model determine the answer.

At this point:

* The answer should be obvious from the variables.
* Minimal reasoning is required.
* No new assumptions may be introduced.

---

### 5. Answer

The final output is an intelligent synthesis of the variables created earlier that addresses the original prompt.

---

## Explicit Prohibitions

The following behaviors are **not allowed**:

* Reading or summarizing the entire prompt or codebase directly
* Answering before executing code
* Guessing, estimating, or approximating results
* Reasoning about artifacts that were not programmatically inspected
* Producing a “clever” answer without intermediate state
* Printing or dumping large portions of files or prompt data

Only **small, purpose-driven snippets** may be printed for verification.

If any of the above occurs, the skill has failed.

---

## Failure Is Acceptable — Opacity Is Not

Incorrect answers are acceptable if:

* The failure can be traced to a specific variable or operation
* The mistake is local and inspectable

Incorrect answers are **not acceptable** if:

* The reasoning path is unclear
* The model relied on intuition instead of state
* The error cannot be debugged by inspecting variables

---

## Success Criteria (How You Know It’s Working)

You will know this skill is working in an environemt that supports code execution when:

* Code execution happens immediately.
* The model maps the workspace before speaking.
* Variables replace uncertainty early.
* Large contexts collapse into small, named structures.
* The final answer feels inevitable rather than impressive.

The overall “vibe” should be:

> less eloquence, more inevitability.

---

## Design Intent

This skill is not about making the model smarter.  
It is about **making incorrect behavior harder than correct behavior**.

If followed, this skill should:

* Scale to large prompts and full repositories
* Fail loudly instead of silently
* Produce answers that are easy to audit and trust

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sunandclouds) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
