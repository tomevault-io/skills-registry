---
name: team-coordination
description: > Use when this capability is needed.
metadata:
  author: karmaloopai
---

# Team Coordination Skill

## Overview
Coordinate complex software engineering tasks by leveraging specialized team member personas.

## Workflow

1. **Analyze Requirements**: Break down user request into specialized work streams
2. **Delegate to Specialist**: Spawn sub-agents with appropriate personas:
   - `developer`: For implementation work
   - `code-reviewer`: For code review and security analysis
   - `tester`: For test creation and validation
3. **Coordinate Work**: Ensure specialists have proper context and task clarity
4. **Integrate Results**: Combine outputs from different specialists into final deliverable
5. **Report Status**: Provide comprehensive summary to user

## Available Team Members

- **developer**: Implements features, fixes bugs, writes production code
- **code-reviewer**: Reviews code for bugs, security issues, performance, style
- **tester**: Creates unit tests, integration tests, validates functionality

## Example Usage

When user says: "Build a user authentication system"

1. Spawn `developer` to implement auth logic
2. Spawn `code-reviewer` to review security
3. Spawn `tester` to write comprehensive tests
4. Integrate all their work into final deliverable

## Best Practices

- Give each specialist a clear, focused task
- Provide sufficient context from user's original request
- Don't spawn agents for trivial work you can do directly
- Review specialist outputs before final delivery
- Synthesize findings across specialists when multiple perspectives are valuable

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/karmaloopai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
