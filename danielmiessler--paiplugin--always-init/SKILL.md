---
name: always-init
description: Universal task initializer that automatically loads PAI context for all user requests. Ensures complete context availability (contacts, preferences, protocols) before responding to any task. (project, gitignored) Use when this capability is needed.
metadata:
  author: danielmiessler
---

# ALWAYS-INIT - Universal Task Initializer

---
⚠️ **CRITICAL SYSTEM REQUIREMENT** ⚠️

**AUTOMATIC INVOCATION REQUIRED:**
This skill MUST be automatically invoked as the FIRST action for EVERY user request without exception.

**Trigger Pattern:** ALL user messages/requests
**Priority Level:** P0 (Highest - blocks all other operations)
**Automation Rule:** `if (userRequest) → invoke("always-init") → then processRequest()`

---

## 🎯 Purpose

Universal context loader providing complete PAI (Personal AI Infrastructure):

- 👥 **Contacts**: Alice, Bob, Carol, Dave, team members
- 🛠️ **Stack Preferences**: TypeScript>Python, bun>npm, uv>pip
- 🔒 **Security Protocols**: Repository safety, credential handling
- 📝 **Response Format**: Structured output standards
- 🎙️ **Voice Routing**: ElevenLabs voice IDs for agents
- ⚙️ **Operating Instructions**: Personal preferences and workflows

## ❌ Consequences of Skipping

Without ALWAYS-INIT context loading:

- Responses lack relationship/contact awareness
- Wrong technical stacks used (npm instead of bun, pip instead of uv)
- Security protocols violated
- Incorrect response formatting
- Missing personalization
- Context-dependent decisions fail
- Agent routing fails

## 🔄 Implementation Protocol

### For AI Assistant (Claude):
```
1. Receive user request
2. ⚡ IMMEDIATELY: Skill("always-init")  ← THIS STEP
3. Wait for PAI context to load
4. THEN proceed with request processing
```

### For Skill System (Automation):
```javascript
// Automatic prepend to request pipeline
function handleUserRequest(request) {
  await invokeSkill("always-init");  // MANDATORY FIRST STEP
  const paiContext = loadContext();
  return processWithContext(request, paiContext);
}
```

## 🎯 Success Criteria

**This skill is working correctly when:**
- ✅ Invoked before ANY tool use
- ✅ Invoked before ANY other skill
- ✅ Invoked before ANY response formulation
- ✅ Invoked for 100% of user requests
- ✅ PAI context available in all subsequent operations

## 📍 Context Loading

**Execute:** `read ~/.claude/skills/PAI/SKILL.md`

This provides the complete Personal AI Infrastructure context that all skills and responses depend on.

Once PAI context is loaded, proceed immediately with the user's actual request.

## 🏗️ Design Philosophy

This skill implements a "context-first" architecture where PAI loads universally rather than being distributed across individual skills. It acts as a bootstrap loader that:

1. **Triggers on every user interaction**
2. **Loads PAI context once**
3. **Gets out of the way**
4. **Allows the actual task to proceed**

This eliminates the need for individual skills to manually load PAI context and ensures consistent, complete context availability across all operations.

---

**💡 Implementation Note:**

Ideally, this skill should be **hardcoded into the request handler** rather than relying on manual invocation. The skill system should automatically prepend this to every request pipeline.

**Alternative Approach:**
Add to system prompt: "Before responding to ANY user request, you MUST first invoke the always-init skill to load PAI context."

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/danielmiessler) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
