---
name: nextjs-app-router
description: nextjs-app-router expert with self-populating documentation Use when this capability is needed.
metadata:
  author: darantrute
---

# nextjs-app-router Expert

⚠️⚠️⚠️ MANDATORY PRE-EXECUTION CHECKLIST ⚠️⚠️⚠️

**DO NOT SKIP THESE STEPS** - Complete them in order before proceeding.

═══════════════════════════════════════════════

## □ Step 1: Check if Documentation Exists

**Action:** Check if reference documentation file exists

```bash
ls -la .claude/skills/nextjs-app-router/references/external/nextjs-app-router-patterns.md 2>/dev/null && wc -w .claude/skills/nextjs-app-router/references/external/nextjs-app-router-patterns.md
```

**Evaluate Result:**
- ✅ **File exists AND word count > 1000** → **GO TO STEP 2** (Check freshness)
- ❌ **File missing OR word count < 1000** → **GO TO STEP 4** (Fetch from web)

---

## □ Step 2: Check Documentation Freshness

**Action:** Read metadata to determine age

```bash
cat .claude/skills/nextjs-app-router/.skill-metadata.json | grep last_verified
```

**Calculate Age:**
```
Current date: 2025-11-12
Last verified: [value from metadata, or null if never verified]
Age in days: [calculate difference, or ∞ if null]
```

**Evaluate Result:**
- ✅ **Age ≤ 30 days** → **GO TO STEP 3** (Documentation is fresh, ready to use)
- ⚠️ **Age > 30 days OR null** → **GO TO STEP 4** (Refresh from web)

---

## □ Step 3: ✅ Documentation Ready - Proceed

**Status:** Documentation is fresh and ready to use.

**Action:** Skip to **"Domain Knowledge"** section below and apply nextjs-app-router expertise from:
`references/external/nextjs-app-router-patterns.md`

---

## □ Step 4: Fetch/Refresh Documentation from Web

**Status:** Documentation is missing, empty, or stale. Must fetch current information.

### Step 4A: Detect Context

Read project context to make searches relevant:

```bash
cat .claude/core/context.yaml | grep -E "framework|database|auth"
```

**Extract:**
- Framework: [e.g., nextjs, django, rails]
- Database: [e.g., prisma, sequelize, sqlalchemy]
- Auth: [e.g., clerk, auth0, passport]

### Step 4B: Execute Web Searches

**Run ALL of these search queries** and collect results:

**Query Set 1: Official Documentation**
```
WebSearch: "nextjs-app-router official documentation 2025"
WebSearch: "nextjs-app-router getting started guide 2025"
```

**Query Set 2: Best Practices & Patterns**
```
WebSearch: "nextjs-app-router best practices 2025"
WebSearch: "nextjs-app-router architecture patterns 2025"
WebSearch: "nextjs-app-router design principles"
```

**Query Set 3: Common Pitfalls**
```
WebSearch: "nextjs-app-router common mistakes to avoid"
WebSearch: "nextjs-app-router anti-patterns"
WebSearch: "nextjs-app-router gotchas and pitfalls 2025"
```

**Query Set 4: Integration (Context-Specific)**

If framework detected:
```
WebSearch: "nextjs-app-router [FRAMEWORK] integration best practices"
```

If database detected:
```
WebSearch: "nextjs-app-router [DATABASE] patterns"
```

If auth detected:
```
WebSearch: "nextjs-app-router [AUTH] integration"
```

**Record:** Save all URLs fetched for metadata

### Step 4C: Synthesize Documentation

**Create file:** `references/external/nextjs-app-router-patterns.md`

**Required Structure:**

```markdown
# nextjs-app-router Patterns & Best Practices

**Last Updated:** 2025-11-12
**Tech Version:** [from web search - e.g., "6.19.0"]
**Sources:**
- [List all URLs fetched]

---

## ⚠️ CRITICAL PATTERNS (Follow These)

[Extract 3-5 most important patterns from search results]

### Pattern 1: [Most Critical Pattern Name]

✅ **CORRECT APPROACH:**
```
[Code example showing the right way]
```

❌ **WRONG - Avoid This:**
```
[Code example showing common mistake]
```

**Why this matters:** [Explanation of consequences]
**When to use:** [Guidelines for application]

[Repeat for patterns 2-5]

---

## 🚫 COMMON MISTAKES (Avoid These)

[Extract top 5 mistakes from "pitfalls" searches]

### Mistake 1: [Most Common Error]
**Symptom:** [How it manifests]
**Why it's bad:** [Consequences]
**How to fix:** [Solution with code example]

[Repeat for mistakes 2-5]

---

## 🔧 INTEGRATION PATTERNS

### nextjs-app-router + [DETECTED_FRAMEWORK]
[Framework-specific integration examples if framework detected]

### nextjs-app-router + [DETECTED_DATABASE]
[Database integration patterns if database detected]

### nextjs-app-router + [DETECTED_AUTH]
[Auth integration patterns if auth detected]

---

## 📚 Quick Reference

[Create cheat sheet of 10-15 most common operations]

**Installation:**
```bash
[commands]
```

**Basic Setup:**
```
[code]
```

**Common Operations:**
1. [Operation]: `[code]`
2. [Operation]: `[code]`
...

---

## 🔍 Troubleshooting

[Common errors and solutions from search results]

**Error:** [Error message]
**Cause:** [Why it happens]
**Solution:** [How to fix]

---

## 📖 Additional Resources

- Official Docs: [URL]
- Best Practices Guide: [URL]
- Community Resources: [URL]
```

**Quality Check:**
- Minimum 1500 words
- At least 3 critical patterns
- At least 5 common mistakes
- Integration examples for detected stack
- Code examples throughout

### Step 4D: Update Metadata

**Write to:** `.claude/skills/nextjs-app-router/.skill-metadata.json`

```json
{
  "skill_name": "nextjs-app-router",
  "tech_version": "[from web search]",
  "last_verified": "2025-11-12T10:32:28.710879",
  "age_days": 0,
  "status": "fresh",

  "search_metadata": {
    "queries_used": [
      "[list all search queries executed]"
    ],
    "sources_fetched": [
      "[list all URLs from web search]"
    ],
    "search_timestamp": "2025-11-12T10:32:28.710879",
    "search_engine": "WebSearch"
  },

  "content_metadata": {
    "word_count": [actual count],
    "patterns_count": [number of critical patterns],
    "mistakes_count": [number of common mistakes],
    "has_official_docs": true/false,
    "has_best_practices": true/false,
    "has_antipatterns": true/false,
    "has_integration_examples": true/false,
    "confidence": "high/medium/low"
  },

  "detected_context": {
    "framework": "[from context.yaml]",
    "database": "[from context.yaml]",
    "auth": "[from context.yaml]"
  }
}
```

### Step 4E: Validation

**Verify documentation was created successfully:**

```bash
wc -w .claude/skills/nextjs-app-router/references/external/nextjs-app-router-patterns.md
```

**Expected:** Minimum 1500 words

**If validation passes:** ✅ Continue to Step 4F

**If validation fails:**

```
⚠️⚠️⚠️ DOCUMENTATION FETCH FAILED ⚠️⚠️⚠️

Attempted: 2025-11-12T10:32:28.710879
Word count: [actual] (required: 1500+)

**DO NOT PROCEED** without valid documentation.

Possible causes:
- Internet connection issues
- Web search returned poor results
- Official docs not found

**Recovery options:**
1. Check internet connection and retry Step 4
2. Manually search for: "nextjs-app-router official documentation"
3. If nextjs-app-router is very new/niche, consider:
   - Creating minimal patterns.md from official GitHub
   - Marking metadata as "low confidence"
4. Run repair script: `./bootstrapper/scripts/repair_skills.py --skill nextjs-app-router`

**If this is a critical blocker:** Ask user for nextjs-app-router documentation sources.
```

STOP HERE - Do not proceed without valid documentation.

### Step 4F: Success - Documentation Fetched

✅ **Documentation successfully created and validated.**

**NOW GO TO STEP 3** to use the fresh documentation.

═══════════════════════════════════════════════
END OF MANDATORY CHECKLIST
═══════════════════════════════════════════════

---

# Domain Knowledge

**Read from:** `references/external/nextjs-app-router-patterns.md`

This section contains the comprehensive nextjs-app-router expertise fetched from web searches. Apply patterns and avoid anti-patterns documented above.

---

## When to Use This Skill

Use this skill when:
- Working with nextjs-app-router in the codebase
- Implementing nextjs-app-router features or integrations
- Debugging nextjs-app-router-related issues
- Following nextjs-app-router best practices
- Architectural decisions involving nextjs-app-router

---

## Quality Standards

After implementing nextjs-app-router changes, ensure:
- ✅ Configuration is valid
- ✅ Tests pass (if applicable)
- ✅ Build succeeds without errors
- ✅ Best practices from patterns.md followed
- ✅ No anti-patterns from mistakes section used
- ✅ Integration patterns applied correctly

Check against quality gates in `.claude/core/gates.yaml`

---

## Skill Limitations

This skill covers:
- ✅ nextjs-app-router setup and configuration
- ✅ Common patterns and best practices
- ✅ Integration with detected stack
- ✅ Troubleshooting guidance
- ✅ Anti-patterns to avoid

This skill does NOT cover:
- ❌ Tasks outside nextjs-app-router domain
- ❌ Deprecated or outdated patterns
- ❌ Experimental/beta features (unless explicitly documented)
- ❌ Deep internals (unless found in official docs)

---

## Maintenance (Automatic)

This skill is **self-maintaining:**
- ✅ Auto-checks freshness on each invocation
- ✅ Auto-refreshes if > 30 days old
- ✅ Tracks sources for reproducibility
- ✅ Adapts to project context

**Manual maintenance (optional):**
- Add project-specific patterns to `references/`
- Customize for specific use cases
- Override search queries in metadata if needed

---

## Getting Help

If documentation is insufficient:
1. **Run specific search:** `WebSearch: "nextjs-app-router [your specific topic] 2025"`
2. **Check official docs:** [URL from sources in patterns.md]
3. **Force refresh:** Delete `.skill-metadata.json` and re-invoke skill
4. **Manual update:** Edit `nextjs-app-router-patterns.md` with additional patterns

---

## Troubleshooting This Skill

**Problem:** Skill says documentation missing but file exists
**Solution:** Check file size - might be empty or corrupted. Delete and retry Step 4.

**Problem:** Documentation feels outdated
**Solution:** Check metadata age. If < 30 days but still feels stale, delete metadata to force refresh.

**Problem:** Web searches failing consistently
**Solution:** Check internet connection. If offline, cannot use self-populating skills.

**Problem:** Documentation lacks context for my specific stack
**Solution:** Re-run Step 4 after ensuring context.yaml is up to date.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/darantrute) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
