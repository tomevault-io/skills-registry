---
name: agile-retrospective
description: When facilitating sprint retrospectives or team improvement sessions. Use when this capability is needed.
metadata:
  author: codermariusz
---

## When to Use
When facilitating sprint retrospectives or team improvement sessions.

## Patterns

### Start/Stop/Continue Format
```markdown
## Sprint X Retrospective

### 🟢 START (new practices)
- [What should we begin doing?]
- [New tools, processes, habits]

### 🔴 STOP (remove friction)
- [What should we stop doing?]
- [What's not working?]

### 🟡 CONTINUE (keep doing)
- [What's working well?]
- [What should we keep?]

### 📋 Action Items
| Action | Owner | Due |
|--------|-------|-----|
| [specific action] | [name] | [date] |
```

### 4Ls Format (Alternative)
```
LIKED:    What went well?
LEARNED:  What did we learn?
LACKED:   What was missing?
LONGED:   What do we wish for?
```

### Facilitation Tips
```
1. Timebox: 45-60 min max
2. Equal voice: everyone speaks
3. No blame: focus on process, not people
4. Action items: max 3, specific, owned
5. Follow-up: review last retro's actions first
```

### Action Item Quality
```
❌ "Improve communication"
✅ "Daily 10min sync at 9am, owner: @lead, starts Monday"

❌ "Write more tests"
✅ "Add integration tests for auth module, owner: @dev, by Sprint end"
```

## Anti-Patterns
- No action items (talk shop only)
- Too many action items (>3 = none done)
- Blame individuals instead of process
- Skip reviewing last sprint's actions
- Manager dominates discussion

## Verification Checklist
- [ ] All team members contributed
- [ ] Max 3 action items
- [ ] Each action has owner + date
- [ ] Previous actions reviewed
- [ ] Notes documented

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/codermariusz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
