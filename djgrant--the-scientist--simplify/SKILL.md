---
name: simplify
description: When a solution needs simplifying, load this skill with the map-reduce skill to find its essence. Use when this capability is needed.
metadata:
  author: djgrant
---

## ::input::

$target: what to simplify
$essence: what must be preserved

## ::context::

$relevantHeuristics: a list of heuristics picked from #heuristics
  
## ::workflow::

FOR $heuristic IN $relevantHeuristics
  USE ~/skill/map-reduce WITH
    $transform: $heuristic
    $validator: check if $essence is preserved
    $strategy: Strategy = "independent"
    $maxIterations: 5
  END
END

## Heuristics

1. Subtractive Iteration: remove one element and check if it still works. 
2. Forced Constraints: reimplement with half the lines and note what survives. 
3. Explain to Caveman: if you can't explain it simply, simplify it. 
4. Reverse Complexity Audit: list every abstraction and name the catastrophe it prevents. 
5. Rewrite Bet: two hours to rewrite from scratch, note what you keep.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/djgrant) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
