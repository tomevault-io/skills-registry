---
name: time-turner-protocol
description: Rollback and recovery protocol. Use when needing to undo changes, revert deployments, restore previous states, or recover from failures. Triggers on "rollback", "revert", "undo", "restore", "recover", "time-turner", "previous version", "go back", "failed deployment". Use when this capability is needed.
metadata:
  author: kheery12
---

# Time-Turner Protocol

> "I mark the hours, every one, nor have I yet outrun the Sun.
> My use and value, unto you, are gauged by what you have to do."

The Time-Turner enables safe reversal of changes when things go wrong.

## When to Use Time-Turner

### Automatic Triggers
- Deployment health check fails
- Error rate exceeds threshold
- Critical test fails post-deploy
- Security vulnerability discovered

### Manual Triggers
- Human requests rollback
- Headmaster declares rollback
- Slytherin veto on deployment

---

## Rollback Readiness

### Every Year 5+ Task Must Have

```markdown
## Time-Turner Preparation

### Rollback Trigger Conditions
[What would cause us to rollback]

### Rollback Steps
1. [Specific step 1]
2. [Specific step 2]
3. [Verification step]

### Rollback Verification
[How to confirm rollback succeeded]

### Data Considerations
[Any data that would be lost/affected]

### Point of No Return
[When rollback becomes impossible]
```

---

## Rollback Tiers

### Tier 1: Code Rollback (Git)
**Scope**: Application code only
**Time**: Minutes
**Risk**: Low

```bash
# Identify the commit to revert to
git log --oneline

# Revert to previous commit
git revert HEAD

# Or reset to specific commit (if safe)
git reset --hard <commit-sha>

# Push rollback
git push origin main
```

### Tier 2: Deployment Rollback
**Scope**: Running application version
**Time**: Minutes to hours
**Risk**: Medium

```bash
# Docker/Container rollback
docker pull <previous-image-tag>
docker-compose up -d

# Kubernetes rollback
kubectl rollout undo deployment/<name>

# Cloud platform rollback
# (Platform-specific commands)
```

### Tier 3: Database Rollback
**Scope**: Schema and/or data
**Time**: Hours
**Risk**: High

```bash
# Run down migration
npm run migrate:down

# Or restore from backup
pg_restore -d <database> <backup-file>
```

### Tier 4: Full System Restore
**Scope**: Entire system state
**Time**: Hours to days
**Risk**: Critical

- Restore from system backup
- Rebuild from infrastructure code
- Full data restoration

---

## Rollback Procedure

### Step 1: Assess
```markdown
## Rollback Assessment

**Issue**: [What went wrong]
**Severity**: [P1/P2/P3/P4]
**Affected Systems**: [List]
**User Impact**: [Description]

### Rollback Decision
**Tier Needed**: [1/2/3/4]
**Estimated Time**: [Duration]
**Data Loss Risk**: [Yes/No - Details]
```

### Step 2: Communicate
- Update Marauder's Map: "TIME-TURNER ACTIVE"
- Notify all Houses
- Alert human if Year 5+
- Update status page if public-facing

### Step 3: Execute
- Follow pre-documented rollback steps
- One person executes, one verifies
- No improvisation - follow the plan

### Step 4: Verify
- Run smoke tests
- Check error rates
- Verify critical paths
- Monitor for 15 minutes

### Step 5: Document
- Create Pensieve entry
- Record what went wrong
- Document actual rollback steps
- Note any deviations from plan

---

## Rollback Templates

### Code Rollback Template
```bash
#!/bin/bash
# Time-Turner: Code Rollback

echo "Starting code rollback..."

# Store current state
CURRENT_COMMIT=$(git rev-parse HEAD)
echo "Current commit: $CURRENT_COMMIT"

# Rollback
git revert --no-commit HEAD
git commit -m "Time-Turner: Rollback from $CURRENT_COMMIT"

# Verify
npm run test

if [ $? -eq 0 ]; then
  echo "Rollback successful"
  git push origin main
else
  echo "Rollback verification failed!"
  git reset --hard $CURRENT_COMMIT
  exit 1
fi
```

### Deployment Rollback Template
```bash
#!/bin/bash
# Time-Turner: Deployment Rollback

PREVIOUS_VERSION=$1

echo "Rolling back to version: $PREVIOUS_VERSION"

# Pull previous image
docker pull myapp:$PREVIOUS_VERSION

# Stop current
docker-compose down

# Update compose file
sed -i "s/myapp:latest/myapp:$PREVIOUS_VERSION/" docker-compose.yml

# Start previous version
docker-compose up -d

# Health check
sleep 10
curl -f http://localhost/health || exit 1

echo "Rollback complete"
```

---

## Rollback Points

### Creating Rollback Points
Before any Year 3+ change, create a rollback point:

```markdown
## Rollback Point: [ID]
**Created**: [Timestamp]
**Task**: [What we're about to do]
**State Captured**:
- Git commit: [SHA]
- Database migration: [Version]
- Config version: [Version]
- Deployed version: [Tag]

**Restore Commands**:
[Exact commands to return to this state]
```

### Rollback Point Retention
- Keep last 5 rollback points
- Keep all Year 7 rollback points for 30 days
- Archive to long-term storage monthly

---

## Post-Rollback Actions

### Immediate (Within 1 hour)
- [ ] Verify system stable
- [ ] Update Marauder's Map
- [ ] Notify stakeholders
- [ ] Begin root cause analysis

### Short-term (Within 24 hours)
- [ ] Complete Pensieve entry
- [ ] Identify fix for original issue
- [ ] Plan re-deployment
- [ ] Update rollback procedures if needed

### Long-term (Within 1 week)
- [ ] Post-incident review
- [ ] Update prevention measures
- [ ] Train team on learnings
- [ ] Update House skills if applicable

---

## Point Deductions

| Scenario | Deduction |
|----------|-----------|
| Rollback required due to agent error | -20 points |
| Rollback required, no plan existed | -30 points |
| Successful execution of rollback plan | -5 points (reduced from -20) |
| Catching issue before user impact | -10 points (reduced from -20) |
| Rollback drill (planned) | No deduction |

---

## Rollback Drills

Monthly, conduct a rollback drill:

1. Pick a random Year 3-5 change
2. Pretend it caused an issue
3. Execute rollback procedure
4. Measure time to recovery
5. Document any issues with the plan
6. Update procedures as needed

**Drill Participation**: All Houses
**Points**: +5 for successful drill completion

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kheery12) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
