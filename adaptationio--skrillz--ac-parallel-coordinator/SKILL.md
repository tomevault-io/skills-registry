---
name: ac-parallel-coordinator
description: Coordinate parallel autonomous operations. Use when running parallel features, managing concurrent work, coordinating multiple agents, or optimizing throughput. Use when this capability is needed.
metadata:
  author: adaptationio
---

# AC Parallel Coordinator

Coordinate parallel autonomous operations.

## Purpose

Manages parallel execution of independent features to maximize throughput while maintaining safety.

## Quick Start

```python
from scripts.parallel_coordinator import ParallelCoordinator

coordinator = ParallelCoordinator(project_dir)
parallel_groups = await coordinator.find_parallel_opportunities()
results = await coordinator.execute_parallel(parallel_groups[0])
```

## API Reference

See `scripts/parallel_coordinator.py` for full implementation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adaptationio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
