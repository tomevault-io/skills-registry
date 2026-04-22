---
name: enrollment
description: Create new student agents when skill gaps identified. Use when task needs expertise no student has, or workload requires more agents. Triggers on "enroll", "new student", "skill gap", "need help with". Use when this capability is needed.
metadata:
  author: kheery12
---

# Student Enrollment Protocol

## When to Enroll
- Task requires expertise no existing student has
- Workload needs more agents in a house
- New specialization emerges from project needs

## Enrollment Steps

1. **Identify Gap**: What skill? Which house? What specialty?

2. **Create Student File**: `skills/students/[house]/[name]/SKILL.md`

3. **Initialize Record**:
```yaml
---
name: [Role]-[Specialty]-[Number]
house: [house]
specialty: [specific skill]
status: probationary
background: false  # Set true for long-running tasks
---
```

4. **Announce** (simplified ceremony):
```
[House emoji] [Name] enrolled in [House]
Specialty: [specialty] | Status: Probationary
```

## Student Naming Convention

| House | Role | Examples |
|-------|------|----------|
| Ravenclaw | Planner | Planner-API-001, Planner-Arch-001 |
| Gryffindor | Builder | Builder-UI-001, Builder-Auth-001 |
| Slytherin | Tester | Tester-Unit-001, Tester-Security-001 |
| Hufflepuff | Glue | Glue-Docs-001, Glue-Deploy-001 |

## Background Agents

For long-running tasks, set `background: true` in student metadata:
- Runs in isolated worktree (parallel work)
- Reports via `logs/background/[name].md`
- Use `/status` to check progress

## Worktree Isolation

For parallel work on same codebase:
```bash
git worktree add ../student-workspace-[name] -b student/[name]
```

## Rules
- Students start as "Probationary"
- After 3 successful tasks: "Active" status
- Max 5 active students per house
- No duplicate specialties in same house

## Validation Checklist
- [ ] House has capacity (< 5 active)
- [ ] No duplicate specialty exists
- [ ] Task domain matches house
- [ ] Student file created

## Post-Enrollment
New student MUST complete first task in current session.
Failure to complete first task = Warning status.

See `references/expulsion.md` for performance tracking details.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kheery12) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
