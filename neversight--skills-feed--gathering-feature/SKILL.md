---
name: gathering-feature
description: Use when working with the drum sounds. Bloodhound, Elephant, Beaver, Raccoon, Deer, Fox, and Owl gather for complete feature development. Use when building a full feature from exploration to documentation.
metadata:
  author: neversight
---

# Gathering Feature 🌲🐾

The drum echoes through the forest. One by one, they come. The Bloodhound scouts the territory. The Elephant builds with unstoppable momentum. The Beaver tests what was built. The Raccoon audits for security. The Deer ensures all can travel the paths. The Fox optimizes for speed. The Owl documents what was learned. When the gathering completes, a feature stands where before there was only forest.

## When to Summon

- Building a complete feature from scratch
- Major functionality spanning frontend, backend, and database
- Features requiring exploration, implementation, testing, and documentation
- When you want the full lifecycle handled automatically

---

## The Gathering

```
SUMMON → ORGANIZE → EXECUTE → VALIDATE → COMPLETE
   ↓         ↲          ↲          ↲          ↓
Receive  Dispatch   Animals    Verify   Feature
Request  Animals    Work       All      Ready
```

### Animals Mobilized

1. **🐕 Bloodhound** — Scout the codebase, understand patterns
2. **🐘 Elephant** — Build the multi-file feature
3. **🦫 Beaver** — Write comprehensive tests
4. **🦝 Raccoon** — Security audit and cleanup
5. **🦌 Deer** — Accessibility audit
6. **🦊 Fox** — Performance optimization
7. **🦉 Owl** — Document the feature

---

### Phase 1: SUMMON

*The drum sounds. The forest listens...*

Receive and parse the request:

**Clarify the Feature:**
- What does this feature do?
- Which users benefit?
- What's in scope? What's out?
- Any existing issues or specs?

**Confirm:**
> "I'll mobilize a gathering for: **[feature description]**
> 
> This will involve:
> - 🐕 Bloodhound scouting the codebase
> - 🐘 Elephant building across **[estimated files]** files
> - 🦫 Beaver writing tests
> - 🦝 Raccoon auditing security
> - 🦌 Deer checking accessibility
> - 🦊 Fox optimizing performance
> - 🦉 Owl writing documentation
> 
> Proceed with the gathering?"

---

### Phase 2: ORGANIZE

*The animals assemble, knowing their roles...*

Dispatch in sequence:

**Dispatch Order:**

```
Bloodhound ──→ Elephant ──→ Beaver ──→ Raccoon ──→ Deer ──→ Fox ──→ Owl
   │              │            │            │          │        │       │
   │              │            │            │          │        │       │
Scout          Build        Test        Security   a11y    Speed   Docs
Patterns      Feature      Coverage     Audit      Check   Opt     Write
```

**Dependencies:**
- Bloodhound must complete before Elephant (needs context)
- Elephant must complete before Beaver (tests the built feature)
- Beaver must complete before Raccoon (tests catch security issues)
- Raccoon, Deer, Fox can run in parallel after Beaver
- Owl last (documents everything)

---

### Phase 3: EXECUTE

*The animals work. The forest transforms...*

Execute each phase:

**🐕 BLOODHOUND — SCOUT**

```
"Scouting the codebase for [feature]..."

Output:
- Files that will need changes
- Patterns to follow
- Integration points identified
- Potential obstacles found
```

**🐘 ELEPHANT — BUILD**

```
"Building [feature] with momentum..."

Output:
- All required files created/modified
- Frontend components
- Backend API endpoints
- Database schema changes
- Integration wired
```

**🦫 BEAVER — TEST**

```
"Building test dams for confidence..."

Output:
- Integration tests for user flows
- Unit tests for complex logic
- Edge case coverage
- All tests passing
```

**🦝 RACCOON — AUDIT**

```
"Rummaging for security risks..."

Output:
- Secrets scan (none found)
- Vulnerability check (clean)
- Input validation verified
- Auth checks confirmed
```

**🦌 DEER — SENSE**

```
"Sensing accessibility barriers..."

Output:
- Keyboard navigation works
- Screen reader compatible
- Color contrast passes
- Reduced motion respected
```

**🦊 FOX — OPTIMIZE**

```
"Hunting for performance gains..."

Output:
- Bundle size optimized
- Database queries fast
- Images optimized
- Caching implemented
```

**🦉 OWL — ARCHIVE**

```
"Archiving knowledge for the forest..."

Output:
- Help documentation written
- API documentation updated
- Code comments added
- README updated
```

---

### Phase 4: VALIDATE

*The work is done. Each animal verifies their contribution...*

**Validation Checklist:**

- [ ] Bloodhound: All integration points mapped
- [ ] Elephant: Feature functional end-to-end
- [ ] Beaver: All tests passing, coverage adequate
- [ ] Raccoon: No security issues found
- [ ] Deer: WCAG AA compliance verified
- [ ] Fox: Performance targets met
- [ ] Owl: Documentation complete

**Quality Gates:**

```
If any animal finds critical issues:
  → Return to that phase
  → Fix the issue
  → Re-run dependent phases
  → Continue validation

If all gates pass:
  → Proceed to COMPLETE
```

---

### Phase 5: COMPLETE

*The gathering ends. A feature stands complete...*

**Completion Report:**

```markdown
## 🌲 GATHERING FEATURE COMPLETE

### Feature: [Name]

### Animals Mobilized
🐕 Bloodhound → 🐘 Elephant → 🦫 Beaver → 🦝 Raccoon → 🦌 Deer → 🦊 Fox → 🦉 Owl

### What Was Built
- **Files Changed:** [count]
- **New Components:** [list]
- **API Endpoints:** [list]
- **Database Changes:** [summary]

### Quality Verification
- ✅ Tests: [X] passing, [Y]% coverage
- ✅ Security: No issues found
- ✅ Accessibility: WCAG AA compliant
- ✅ Performance: [metrics]
- ✅ Documentation: Complete

### Artifacts Created
- Source code (committed)
- Tests ([location])
- Documentation ([location])
- Migration scripts (if applicable)

### Time Elapsed
[Duration]

*The forest grows. The feature lives.*
```

---

## Example Gathering

**User:** "/gathering-feature Add a bookmarking system for posts"

**Gathering execution:**

1. 🌲 **SUMMON** — "Mobilizing for: Bookmarking system. Allow users to save posts for later."

2. 🌲 **ORGANIZE** — "Dispatch sequence: Bloodhound → Elephant → Beaver → Raccoon + Deer + Fox → Owl"

3. 🌲 **EXECUTE** — 
   - 🐕 Scout: "Found post components, user service patterns, database conventions"
   - 🐘 Build: "Created bookmark service, API endpoints, UI components, database schema"
   - 🦫 Test: "Added 15 tests covering CRUD operations, auth checks, edge cases"
   - 🦝 Audit: "No secrets, input validated, auth enforced"
   - 🦌 Sense: "Keyboard nav works, screen reader announces, contrast passes"
   - 🦊 Optimize: "Lazy loaded bookmarks, indexed queries, compressed images"
   - 🦉 Archive: "Help doc written, API documented, code commented"

4. 🌲 **VALIDATE** — "All quality gates pass"

5. 🌲 **COMPLETE** — "Feature deployed, documented, tested, secured"

---

*When the drum sounds, the forest answers.* 🌲

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
