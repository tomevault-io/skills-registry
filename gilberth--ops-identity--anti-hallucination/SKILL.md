---
name: anti-hallucination
description: Validates AI prompts and LLM findings against source data to prevent hallucinations. Use when: (1) Adding new analysis prompts to server.js, (2) Reviewing prompts for hallucination risks, (3) Implementing validation rules in ATTRIBUTE_VALIDATION_RULES, (4) Auditing that findings match source JSON data
metadata:
  author: gilberth
---

# Anti-Hallucination Validation for OpsIdentity

## Overview

This skill ensures LLM-generated findings are grounded in actual data. It prevents the AI from inventing object names, inflating counts, or claiming attributes that don't exist in the source JSON.

## When to Use This Skill

Invoke this skill when:
1. Adding NEW analysis prompts to `server.js`
2. Reviewing EXISTING prompts for hallucination risks
3. Implementing NEW `ATTRIBUTE_VALIDATION_RULES`
4. Debugging findings that contain suspicious data

## Quick Validation Checklist

Before deploying any prompt changes, verify:

### In the Prompt (server.js)
- [ ] Contains `⚠️ REGLA ANTI-ALUCINACIÓN: Solo reporta objetos que aparezcan EXPLÍCITAMENTE en los datos`
- [ ] Specifies `type_id` for each finding type
- [ ] Requires `affected_objects con nombres REALES`
- [ ] Limits objects: `máximo 10, luego "...y X más"`

### In ATTRIBUTE_VALIDATION_RULES (server.js)
- [ ] Every `type_id` has a corresponding validation rule
- [ ] Rule includes correct `category` and `identifierField`
- [ ] `validate` function checks actual attributes
- [ ] For nested data, includes `validateAffectedObject`

## Audit Commands

```bash
# Check prompts have anti-hallucination rules
grep -c "ANTI-ALUCINACIÓN" server/server.js

# List all validation rules
grep -E "'[A-Z_]+': \{" server/server.js | wc -l

# Check validation is being called
grep "validateFindings\|validateAttributes" server/server.js

# Find hallucination blocking logs
grep "BLOCKING.*HALLUCINATION" server/server.js
```

## Validation Rule Template

```javascript
'NEW_FINDING_TYPE_ID': {
  category: 'CategoryName',
  identifierField: 'Name',
  validate: (obj) => obj.Enabled && obj.RiskyAttribute === true,
  // For nested data structures:
  validateAffectedObject: (objName, parentObj) => {
    return parentObj.NestedArray?.some(item =>
      item.toLowerCase().includes(objName.toLowerCase())
    );
  }
}
```

## Common Hallucination Patterns

| Pattern | Detection | Action |
|---------|-----------|--------|
| Invented names | Object not in source data | 🛑 BLOCK |
| Inflated counts | `affected_count` > `affected_objects.length` | ⚠️ FIX |
| Wrong attributes | Object exists but attribute value differs | 🛑 BLOCK |
| Generic names | Contains "test", "ejemplo", "sample" | ⚠️ FLAG |

## Integration with Workflow

This skill enforces Step 4 of the mandatory workflow in CLAUDE.md:

```
PS1 → LLM Prompt → DOCX → [Anti-Hallucination Validation]
                              ↓
                    validateFindings()
                    validateAttributes()
                    validateAffectedObject()
```

## Reference Documentation

Read [`anti-hallucination.md`](anti-hallucination.md) for complete validation patterns and implementation details.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gilberth) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
