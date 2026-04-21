---
name: architecture-lookup
description: > Use when this capability is needed.
metadata:
  author: vperreard
---

# Architecture Index - Quick Reference

**Token Efficiency**: 500 tokens vs 15k for full ERD
**Last Update**: 30 January 2026

## Quick Stats
- **142 models** across 10+ domains
- **97 enums** categorized
- **PascalCase** convention (Oct 2025 migration)

---

## Models by Domain

### Planning & Scheduling (28 models)
- Core: Assignment, BlocDayPlanning, PlanningJob
- V2: PlanningV2Assignment, PlanningV2Conflict
- Templates: TrameModele, AffectationModele, PersonnelRequisModele
- Legacy: TrameAffectation, RegularAssignment, PlanningSession

### Business Rules (22 models)
- **Phase 2 BDD (6 types - Oct 2025)**:
  - ContinuityRule (rest after guards)
  - EquityRuleV2 (fair distribution)
  - InterSectorRule (cross-sector rules)
  - MaxHoursRule, MinRestRule, SkillMatchingRule
- Legacy: EquityRule, SupervisionRule

### Leave Management (12 models)
- Core: PlannedAbsence, LeaveBalance, LeaveTypeSetting
- Quota: QuotaCarryOver, QuotaTransfer
- Legacy: Absence

### Site & Infrastructure (8 models)
- Site, OperatingRoom, OperatingSector
- Department, Location, ActivityType

### User & Auth (11 models)
- User, Personnel, RefreshToken, LoginLog
- Skill, UserSkill, WorkContract
- ApprovalRequest

---

## Enums by Category

### Assignment & Planning
- AssignmentType: GARDE, ASTREINTE, BLOC_PROGRAMME, CONSULTATION, REPOS, CONGE
- AssignmentStatus: PENDING, CONFIRMED, CANCELLED, COMPLETED
- Period: MATIN, APRES_MIDI, NUIT, JOURNEE, GARDE_24H

### Leave Management
- LeaveType: CP, RTT, FORMATION, MALADIE, CONGE_PARENTAL, MATERNITE, PATERNITE
- LeaveStatus: PENDING, APPROVED, REJECTED, CANCELLED

### Professional Roles
- ProfessionalRole: MAR, IADE, CHIRURGIEN, CADRE, ADMINISTRATIF

### Business Rules
- RuleType: CONTINUITY, EQUITY, SUPERVISION, MAX_HOURS, MIN_REST, SKILL_MATCHING
- RulePriority: CRITICAL, HIGH, MEDIUM, LOW

---

## Decision Tree

```
New feature requested
  ↓
1. Check models by domain (this file - 500 tokens)
  ↓
2. Model exists in category?
   YES → Read specific ERD section (~200 tokens)
   NO  → Continue
  ↓
3. Enum exists?
   YES → Reuse
   NO  → Justify new creation
  ↓
4. Check DONT_DO.md if complex pattern
```

---

## Progressive Resources

For complete listings:
- **`resources/models-by-domain.md`** - All 142 models with details
- **`resources/enums-reference.md`** - All 97 enums with values

**Reference**: ARCHITECTURE_INDEX.md, ENTITY_RELATIONSHIP_DIAGRAM.md
**Last Update**: 30 January 2026

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vperreard) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
