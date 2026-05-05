---
name: gathering-architecture
description: Use when working with the drum sounds. Eagle, Swan, and Elephant gather for system architecture. Use when designing major systems from vision to implementation.
metadata:
  author: neversight
---

# Gathering Architecture 🌲🦅

The drum echoes high in the canopy. The Eagle soars above, seeing the forest's patterns. The Swan glides across the lake, elegant designs taking form. The Elephant moves below, building what was envisioned. Together they transform a clearing into a cathedral of code—systems that stand for seasons.

## When to Summon

- Designing new systems or services
- Major architectural decisions
- Refactoring core infrastructure
- Creating platforms that other features build upon
- When vision, design, and implementation must align

---

## The Gathering

```
SUMMON → ORGANIZE → EXECUTE → VALIDATE → COMPLETE
   ↓         ↲          ↲          ↲          ↓
Receive  Dispatch   Animals    Verify   Architecture
Request  Animals    Work       Design   Defined
```

### Animals Mobilized

1. **🦅 Eagle** — Design system architecture from 10,000 feet
2. **🦢 Swan** — Write detailed technical specifications
3. **🐘 Elephant** — Implement the architectural foundation

---

### Phase 1: SUMMON

*The drum sounds. The canopy rustles...*

Receive and parse the request:

**Clarify the System:**
- What problem does this solve?
- What are the scale requirements?
- What are the constraints?
- What's the growth trajectory?

**Nature Metaphor:**
> "Every architecture needs a nature metaphor. What does this system resemble?
> - Heartwood (core that holds everything)
> - Wisp (gentle guiding light)
> - Porch (place to gather and talk)
> - Something else?"

**Confirm:**
> "I'll mobilize an architecture gathering for: **[system description]**
> 
> This will involve:
> - 🦅 Eagle designing the high-level architecture
> - 🦢 Swan writing the detailed specification
> - 🐘 Elephant implementing the foundation
> 
> Proceed with the gathering?"

---

### Phase 2: ORGANIZE

*The birds circle. The elephant waits below...*

Dispatch in sequence:

**Dispatch Order:**

```
Eagle ──→ Swan ──→ Elephant
  │         │           │
  │         │           │
Design    Write      Build
System    Spec       Foundation
```

**Dependencies:**
- Eagle must complete before Swan (needs architecture vision)
- Swan must complete before Elephant (needs detailed spec)

---

### Phase 3: EXECUTE

*The architecture takes form from sky to earth...*

Execute each phase:

**🦅 EAGLE — DESIGN**

```
"Soaring above to see the whole system..."

Output:
- System boundaries defined
- Component interactions mapped
- Technology choices documented
- Scale and constraints identified
- Nature metaphor chosen

Artifacts:
- Architecture overview document
- System diagrams
- ADR (Architecture Decision Records)
```

**🦢 SWAN — SPECIFY**

```
"Gliding across to craft the specification..."

Output:
- Detailed technical specification
- API contracts defined
- Database schema designed
- Flow diagrams created
- ASCII art header
- Implementation checklist

Artifacts:
- tech-spec.md with all required sections
- Interface definitions
- Migration plan (if applicable)
```

**🐘 ELEPHANT — BUILD**

```
"Building the foundation with unstoppable momentum..."

Output:
- Core infrastructure implemented
- Base classes/modules created
- API skeleton established
- Database migrations written
- Essential tests included

Artifacts:
- Working codebase
- Foundation tests
- Setup documentation
```

---

### Phase 4: VALIDATE

*The structure stands. Each animal verifies their work...*

**Validation Checklist:**

- [ ] Eagle: Architecture addresses all requirements
- [ ] Swan: Specification is complete and implementable
- [ ] Elephant: Foundation is solid and tested

**Review Points:**

```
After Eagle:
  → Review architecture with stakeholders
  → Confirm boundaries and trade-offs
  → Approve before Swan begins

After Swan:
  → Review spec for completeness
  → Verify all sections present
  → Confirm implementation ready

After Elephant:
  → Test foundation thoroughly
  → Verify patterns established
  → Confirm next features can build upon it
```

---

### Phase 5: COMPLETE

*The gathering ends. Architecture stands ready...*

**Completion Report:**

```markdown
## 🌲 GATHERING ARCHITECTURE COMPLETE

### System: [Name]

### Animals Mobilized
🦅 Eagle → 🦢 Swan → 🐘 Elephant

### Architecture Decisions
- **Pattern:** [e.g., Event-driven microservices]
- **Scale Target:** [e.g., 10k concurrent users]
- **Key Trade-offs:** [summary]

### Artifacts Created
- Architecture Overview (`docs/architecture/[system].md`)
- Technical Specification (`docs/specs/[system]-spec.md`)
- ADRs ([list])
- Foundation Code ([location])
- Base Tests ([location])

### Ready for
- Feature development on this foundation
- Team onboarding using the spec
- Future architecture reviews

### Time Elapsed
[Duration]

*The forest has a new landmark.* 🌲
```

---

## Example Gathering

**User:** "/gathering-architecture Design the notification system"

**Gathering execution:**

1. 🌲 **SUMMON** — "Mobilizing for: Notification system. Send email, push, SMS to users. Scale: millions of notifications/day."

2. 🌲 **ORGANIZE** — "Sequence: Eagle (architecture) → Swan (spec) → Elephant (foundation)"

3. 🌲 **EXECUTE** —
   - 🦅 Eagle: "Event-driven: App emits events → Queue → Workers → Providers. Scales horizontally."
   - 🦢 Swan: "Complete spec with flow diagrams, API contracts, provider adapter interface"
   - 🐘 Elephant: "Event bus, queue infrastructure, base notification service, database schema"

4. 🌲 **VALIDATE** — "Architecture handles scale, spec complete, foundation tested"

5. 🌲 **COMPLETE** — "Notification platform ready for feature development"

---

*From vision to foundation, the forest grows.* 🌲

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
