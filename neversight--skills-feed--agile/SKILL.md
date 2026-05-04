---
name: agile
description: Agile product management, Scrum practices, and team collaboration for iterative product development. Use when this capability is needed.
metadata:
  author: neversight
---

# Agile Product Management Skill

Apply agile methodologies to product development and team collaboration. Master sprint planning, backlog management, and continuous improvement.

## Scrum Framework

### Sprint Cycle (2-week)

| Day | Ceremony | Duration | Attendees |
|-----|----------|----------|-----------|
| Mon | Sprint Planning | 2h | Full team |
| Daily | Standup | 15min | Full team |
| Thu | Design Review | 1h | PM, Design, Eng |
| Fri | Sprint Review | 1h | Full team + stakeholders |
| Fri | Retrospective | 30min | Full team |

### Sprint Planning Template

```
Sprint Goal: [One sentence describing sprint outcome]

Capacity:
- Team size: 5 engineers
- Working days: 10
- Available points: 40

Selected Stories:
1. [Story A] - 8 points - Owner: Dev1
2. [Story B] - 5 points - Owner: Dev2
3. [Story C] - 13 points - Owner: Dev3
...

Risks:
- [Risk 1]: Mitigation plan
- [Risk 2]: Mitigation plan

Definition of Done:
[ ] Code reviewed
[ ] Tests written
[ ] Documentation updated
[ ] PM accepted
```

## PM in Agile

### User Story Format (INVEST)

```
As a [user type]
I want [action/capability]
So that [benefit/outcome]

Acceptance Criteria:
Given [context]
When [action]
Then [result]
```

### Backlog Grooming

**Weekly Grooming (1 hour):**
1. Review upcoming sprint stories
2. Clarify acceptance criteria
3. Size/estimate stories
4. Identify dependencies
5. Reorder by priority

### Story Points Guide

| Points | Complexity | Example |
|--------|------------|---------|
| 1 | Trivial | Config change |
| 2 | Small | Minor UI tweak |
| 3 | Medium | New simple feature |
| 5 | Large | Complex feature |
| 8 | XL | Multiple components |
| 13 | Epic | Should be broken down |

## Kanban

### Board Structure

```
| Backlog | To Do | In Progress | Review | Done |
|---------|-------|-------------|--------|------|
|         |       |             |        |      |
|  WIP: - | WIP:3 |   WIP: 3    | WIP: 2 |  -   |
```

### Flow Metrics

- **Lead Time**: Request → Done
- **Cycle Time**: In Progress → Done
- **Throughput**: Items completed per week
- **WIP**: Work in progress items

## Scaling Agile

### SAFe Basics

- **Team Level**: Scrum teams (5-9 people)
- **Program Level**: Multiple teams (50-125 people)
- **Large Solution**: Multiple programs
- **Portfolio**: Business strategy

### Cross-Team Coordination

- **Scrum of Scrums**: Daily sync between teams
- **PI Planning**: Quarterly planning event
- **System Demo**: Integration showcase
- **Inspect & Adapt**: Quarterly retrospective

## Troubleshooting

### Yaygın Hatalar & Çözümler

| Hata | Olası Sebep | Çözüm |
|------|-------------|-------|
| Sprint overcommitment | Unrealistic capacity | Track velocity, be honest |
| Carryover stories | Scope creep | Stricter acceptance |
| Long standups | Too much detail | Time-box, parking lot |
| No retro action | No ownership | Assign owners, follow up |

### Debug Checklist

```
[ ] Sprint goal clearly defined mi?
[ ] Stories properly sized mi?
[ ] WIP limits respected mi?
[ ] Retro actions implemented mi?
[ ] Velocity tracked mi?
```

### Recovery Procedures

1. **Sprint Failure** → Retrospective deep-dive, reset
2. **Team Burnout** → Reduce scope, add slack time
3. **Velocity Drop** → Investigate blockers, 1:1s

## Learning Outcomes

- Run effective sprints
- Write well-formed user stories
- Manage backlogs efficiently
- Facilitate productive ceremonies
- Track and improve velocity

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
