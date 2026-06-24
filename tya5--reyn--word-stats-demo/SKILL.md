---
name: word-stats-demo
description: | Use when this capability is needed.
metadata:
  author: tya5
---

## Overview

A minimal demonstration of the `python` preprocessor step. The phase
declares a Python function that runs in pure mode (sandboxed), computes
deterministic text statistics, and injects them into the artifact under
`data.stats`. The LLM then writes a commentary that references those
exact numbers — something LLMs are otherwise unreliable at.

## Input

`user_message` text — any string.

## Output

`text_review.commentary` — short prose discussing the input through the
lens of the precomputed statistics.

## Why this is a good fit for python

LLMs are bad at counting characters / tokens / lines accurately. Python
counts them precisely in microseconds. By doing the count in Python and
showing the result to the LLM, the commentary stays factual.

---
> Source: [tya5/reyn](https://github.com/tya5/reyn) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
