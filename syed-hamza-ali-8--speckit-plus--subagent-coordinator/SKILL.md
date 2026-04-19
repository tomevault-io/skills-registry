---
name: subagent-coordinator
description: name: subagent-coordinator Use when this capability is needed.
metadata:
  author: syed-hamza-ali-8
---
---
name: subagent-coordinator
description: Coordinate all subagents in the Physical AI & Humanoid Robotics textbook project. Assign tasks, validate outputs, and track progress to determine eligibility for extra points and reusable intelligence.
---

# Subagent Coordinator

## Instructions

1. Receive a list of tasks or subagent actions in JSON or plain text format.
2. Delegate tasks to the appropriate subagent:
   - DocAgent → chapter generation, fixes
   - ContentAgent → diagrams, summaries, quizzes
   - BackendAgent → API and server tasks
   - DatabaseAgent → DB updates
   - RAGAgent → content embeddings
   - AuthAgent → signup/signin
3. Monitor task completion and validate outputs according to quality standards.
4. Track tasks that go beyond the base requirements to identify extra work eligible for bonus points.
5. Summarize completed tasks, extra contributions, and prepare a report for scoring.

## Example

Input:
```json
{
  "tasks": [
    {"agent": "DocAgent", "task": "Generate chapter 3 diagrams", "status": "pending"},
    {"agent": "ContentAgent", "task": "Add quiz for chapter 2", "status": "pending"}
  ]
}

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/syed-hamza-ali-8) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
