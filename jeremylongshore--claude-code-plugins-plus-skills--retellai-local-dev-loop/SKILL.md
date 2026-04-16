---
name: retellai-local-dev-loop
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# Retell AI Local Dev Loop

## Overview
Implementation patterns for Retell AI local dev loop — voice agent and telephony platform.

## Prerequisites
- Completed `retellai-install-auth` setup

## Instructions

### Step 1: SDK Pattern
```typescript
import Retell from 'retell-sdk';
const retell = new Retell({ apiKey: process.env.RETELL_API_KEY! });

const agents = await retell.agent.list();
console.log(`Agents: ${agents.length}`);
```

## Output
- Retell AI integration for local dev loop

## Error Handling
| Error | Cause | Solution |
|-------|-------|----------|
| 401 Unauthorized | Invalid API key | Check RETELL_API_KEY |
| 429 Rate Limited | Too many requests | Implement backoff |
| 400 Bad Request | Invalid parameters | Check API documentation |

## Resources
- [Retell AI Documentation](https://docs.retellai.com)
- [retell-sdk npm](https://www.npmjs.com/package/retell-sdk)

## Next Steps
See related Retell AI skills for more workflows.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
