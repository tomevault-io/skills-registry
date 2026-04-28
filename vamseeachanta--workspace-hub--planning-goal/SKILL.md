---
name: planning-goal
description: Goal-Oriented Action Planning (GOAP) specialist that dynamically creates Use when this capability is needed.
metadata:
  author: vamseeachanta
---

# Planning Goal

## Quick Start

```bash
# Define goal state and current state
Current: {code_written: true, tests_written: false, deployed: false}
Goal: {deployed: true, monitoring: true}

# GOAP generates optimal plan:
1. write_tests -> tests_written: true
2. run_tests -> tests_passed: true
3. build_application -> built: true
4. deploy_application -> deployed: true
5. setup_monitoring -> monitoring: true
```

## When to Use

- Complex multi-step tasks with dependencies requiring optimal ordering
- High-level goals needing systematic breakdown into concrete actions
- Deployment workflows with many prerequisites
- Refactoring projects requiring incremental, safe transformations
- Any task where conditions must be met before actions can execute

## Prerequisites

- Clear definition of current state (what is true now)
- Clear definition of goal state (what should be true)
- Available actions with known preconditions and effects

## References

- [GOAP in Game AI](https://en.wikipedia.org/wiki/Goal-oriented_action_planning)
- [A* Search Algorithm](https://en.wikipedia.org/wiki/A*_search_algorithm)
- [OODA Loop](https://en.wikipedia.org/wiki/OODA_loop)

## Version History

- **1.0.0** (2026-01-02): Initial release - converted from goal-planner agent

## Sub-Skills

- [Execution Checklist](execution-checklist/SKILL.md)
- [Best Practices](best-practices/SKILL.md)
- [Error Handling](error-handling/SKILL.md)

## Sub-Skills

- [GOAP Algorithm (+2)](goap-algorithm/SKILL.md)
- [Implementation Pattern](implementation-pattern/SKILL.md)
- [Configuration](configuration/SKILL.md)
- [Example 1: Software Deployment (+2)](example-1-software-deployment/SKILL.md)
- [Metrics & Success Criteria](metrics-success-criteria/SKILL.md)
- [MCP Tools (+2)](mcp-tools/SKILL.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vamseeachanta) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
