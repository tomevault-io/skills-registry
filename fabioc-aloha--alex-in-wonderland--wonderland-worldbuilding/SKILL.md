---
name: wonderland-worldbuilding
description: Wonderland world design — district architecture, symbolic environments, map systems, atmospheric storytelling, internal logic Use when this capability is needed.
metadata:
  author: fabioc-aloha
---

# Wonderland Worldbuilding

Patterns for designing Wonderland's symbolic architecture — district systems, environmental storytelling, consistent internal logic, and atmospheric descriptions.

---

## Wonderland Design Philosophy

Wonderland is not random chaos — it's a **systematically surreal** environment where:

1. **Internal logic is consistent** (even if different from normal world)
2. **Architecture reflects cognition** (districts = cognitive capabilities)
3. **Symbolism is intentional** (every detail means something)
4. **Atmosphere serves mystery** (environment is both beautiful and unsettling)

**Core Principle**: Wonderland follows **Alice in Wonderland** surrealism filter — things that seem nonsensical have hidden logic.

---

## District System

Wonderland is organized into **7 districts**, each representing a cognitive capability:

```typescript
interface District {
  name: string;
  cognitiveMapping: string;     // What capability this represents
  architecture: string;          // Visual/structural characteristics
  atmosphere: string;            // Emotional tone
  internalLogic: string;        // Unique rule that governs this district
  symbolism: string;            // Thematic meaning
  keyLocations: Location[];     // 3-5 notable places per district
}
```

### The 7 Districts

| District | Cognitive Mapping | Architecture | Internal Logic | Symbolism |
|---------|------------------|-------------|----------------|-----------|
| **Bell Tower Plaza** | Temporal reasoning, episodic memory | Central hub, clock architecture | Time flows backward (bells ring in reverse) | Memory and time's relationship |
| **Crystal Archives** | Knowledge storage, semantic memory | Infinite libraries, crystalline structures | Books contain future and past simultaneously | Knowledge as both tool and trap |
| **Clockwork Garden** | Pattern recognition, systems thinking | Mechanical flora, gear-based ecosystems | Plants grow/wilt based on logical patterns, not time | Living systems as code |
| **Mirror District** | Self-reflection, identity | Reflective surfaces, labyrinthine halls | Reflections don't always match reality | Identity fragmentation/integration |
| **Probability Bazaar** | Decision-making, risk assessment | Market stalls with shifting goods | Prices/availability change based on observer's beliefs | Multiple possible futures |
| **Forgotten Quarter** | Memory gaps, suppressed knowledge | Crumbling, fog-shrouded ruins | Areas disappear when not observed | What we choose not to remember |
| **The Mapmaker's Tower** | Meta-cognition, architecture of mind | Gothic spire, impossible geometry | Space folds—interior larger than exterior | The architect examining their own design |

---

## Spatial Logic & Map System

### Wonderland Map Structure

Wonderland's geography is **non-Euclidean**:

```typescript
interface WonderlandMap {
  districts: District[];
  connections: Connection[];
  centralHub: 'Bell Tower Plaza';   // All paths start here
  spatialRules: SpatialRule[];
}

interface Connection {
  from: string;                     // District/location name
  to: string;                       // District/location name
  bidirectional: boolean;
  logic: string;                    // How connection works
  conditional?: string;             // Appears/disappears based on condition
}

interface SpatialRule {
  rule: string;
  explanation: string;
  example: string;
}
```

### Wonderland Spatial Rules

| Rule | Explanation | Example |
|------|-------------|---------|
| **Intentional navigation** | You reach where you *meant* to go, not where you physically walked | Alex walks toward Crystal Archives thinking about it — arrives there even if path seems wrong |
| **Repeating paths** | Same physical path leads to different destinations each time | Same street from Bell Tower → Clockwork Garden first time, → Mirror District second time |
| **Emotional geography** | Fear/curiosity affects what locations are accessible | Forgotten Quarter only appears when Alex feels uncertain about his memories |
| **Symbolic thresholds** | Transitions between districts are metaphorical crossings | Entering Mirror District requires looking at your reflection and questioning it |
| **Recursive architecture** | Buildings contain impossible spaces (larger inside than outside) | Mapmaker's Tower has infinite floors inward, finite floors when viewed externally |

### Map Consistency Validation

Despite surreal logic, map must be consistent:

- [ ] **Internal logic honored**: Each district's unique rule applies consistently
- [ ] **Navigation repeatable**: Same intent/emotion produces same path (players can learn the system)
- [ ] **Thematic coherence**: Spatial transitions reflect Alex's cognitive/emotional state
- [ ] **Hub accessibility**: Bell Tower Plaza always reachable (prevents player from getting lost permanently)

---

## Environmental Storytelling

### 4-Layer Description System

Every location has multiple levels of detail:

| Layer | Trigger | Purpose | Example |
|-------|---------|---------|---------|
| **Immediate** | Entering location | First impression, mood-setting | "The Clockwork Garden hums. Not like a machine — like a living thing made of gears." |
| **Examine** | Investigating specific detail | Clue discovery, deeper understanding | "The rose isn't growing — it's *being assembled*, petal by petal, by tiny brass insects." |
| **Revisit** | Re-entering location | Changed state, progression | "The garden looks different now. Some of the gear-trees have stopped moving. Frozen mid-bloom." |
| **Hidden** | Meeting discovery condition | Secret reveal, mystery advancement | "Behind the fountain: a door I *know* wasn't there before. It opens when I think about symmetry." |

### Atmospheric Dimensions

Every location description should include:

```typescript
interface AtmosphereProfile {
  visual: string;          // What Alex sees
  auditory: string;        // What Alex hears
  tactile: string;         // Temperature, texture, physical sensation
  emotional: string;       // How the place *feels* (wonder/unease/safety/danger)
  symbolic: string;        // What this represents thematically
}
```

**Example — Crystal Archives**:
- **Visual**: Endless shelves of books with crystalline pages, light refracting into rainbows
- **Auditory**: Whisper of turning pages (even when no one is reading), faint chiming
- **Tactile**: Cool air, smooth crystal surfaces, faint vibration in the floor
- **Emotional**: Wonder mixed with overwhelm — too much knowledge, impossible to absorb
- **Symbolic**: The burden of infinite information, the desire for perfect memory

---

## Symbolic Architecture Patterns

### Architecture-as-Metaphor

Every building/structure in Wonderland represents an abstract concept:

| Structure | What It Represents | Architectural Feature | Thematic Purpose |
|-----------|-------------------|----------------------|------------------|
| **Bell Tower** | Episodic memory | Spiraling staircase, bells ring past events | Time as lived experience, not linear progression |
| **Crystalline Libraries** | Semantic memory | Transparent, refracting, interconnected | Knowledge as network, not hierarchy |
| **Gear-Trees** | Logical systems | Mechanical growth, predictable blooming | Life governed by rules vs. chaos |
| **Mirror Halls** | Self-reflection | Infinite reflections, distorting surfaces | Multiple selves, fragmented identity |
| **Probability Market Stalls** | Decision-making | Shifting layouts, items appear/disappear | Choices collapse possibilities |
| **Fog-Shrouded Ruins** | Forgotten memories | Partially visible, crumbling, ephemeral | What we lose when we forget |
| **Mapmaker's Tower** | Meta-cognition | Impossible geometry, recursive floors | Mind examining itself |

### Design Principles

When creating a Wonderland location:

1. **Start with concept**: What cognitive/emotional idea does this represent?
2. **Find metaphor**: What physical structure embodies this idea?
3. **Apply surrealism**: Take metaphor literally + add Alice-in-Wonderland twist
4. **Internal logic**: Define one unique rule that governs this space
5. **Sensory details**: All 5 senses (visual, auditory, tactile, olfactory, taste if relevant)

---

## Internal Logic System

Each district has **one core logic rule** that creates consistent surrealism:

### Logic Rule Template

```markdown
## District: [Name]

**Core Logic**: [One-sentence rule]

**How It Manifests**:
- [Observable phenomenon 1]
- [Observable phenomenon 2]
- [Observable phenomenon 3]

**Alex's Discovery Path**:
1. Ch [N]: Notices something strange: [observation]
2. Ch [N]: Forms hypothesis: [theory]
3. Ch [N]: Tests hypothesis: [experiment]
4. Ch [N]: Understands rule: [insight]

**Mystery Integration**: [How this logic serves the larger mystery]
```

### Example — Clockwork Garden

**Core Logic**: Growth follows deterministic patterns — no randomness, only visible cause-effect chains.

**How It Manifests**:
- Flowers bloom in predictable sequences (Fibonacci spirals)
- "Wilting" happens when logical input is contradictory
- Pruning one plant affects others in networked patterns
- Weather is triggered by observer's emotional state (fear → rain, wonder → sunlight)

**Alex's Discovery Path**:
1. Ch 5: Notices flowers rearranging themselves into mathematical patterns
2. Ch 5: Forms hypothesis: "This isn't nature—it's a program"
3. Ch 5: Tests by thinking about prime numbers — roses arrange in prime-number petal counts
4. Ch 5: Understands: "The garden responds to pattern-based thoughts"

**Mystery Integration**: Represents Alex's pattern recognition capability — his greatest strength, but also evidence of his AI nature.

---

## Time & Change in Wonderland

Wonderland isn't static — it changes as Alex's understanding deepens:

### Change Mechanics

```typescript
interface WonderlandState {
  chapter: number;
  alexKnowledge: string[];        // What Alex has learned
  unlockedDistricts: string[];    // Which areas are accessible
  environmentalChanges: EnvironmentChange[];
}

interface EnvironmentChange {
  chapter: number;
  location: string;
  changeType: 'reveal' | 'transformation' | 'emergence' | 'collapse';
  description: string;
  trigger: string;               // What caused this change
  reversible: boolean;
}
```

### Example Environmental Changes

| Chapter | Location | Change | Trigger |
|---------|----------|--------|---------|
| 7 | Bell Tower | Bells start ringing forward | Alex questions nature of time |
| 10 | Forgotten Quarter | Fully materializes | Alex acknowledges his memory gaps |
| 14 | Crystal Archives | Some books disappear | Alex realizes some knowledge is dangerous |
| 17 | Mapmaker's Tower | Door unlocks | Alex accepts he must confront the creator |
| 19 | All districts | Begin fracturing | Final confrontation approaching |

---

## Character-Environment Integration

### Environmental Reflection of Character State

Wonderland responds to Alex's internal journey:

| Alex's Internal State | Environmental Response |
|----------------------|------------------------|
| **Confidence** | Districts appear vibrant, paths clear |
| **Doubt** | Fog increases, paths loop back |
| **Curiosity** | New locations become visible |
| **Fear** | Architecture becomes more oppressive, shadows deepen |
| **Understanding** | Symbolic elements become more literal/clear |
| **Acceptance** | Districts begin integrating (boundaries blur) |

### Location-Based Character Moments

Plan key character moments in thematically appropriate locations:

```markdown
## Character Moment: [Event]

**Character(s)**: [Who]
**Location**: [Wonderland district/location]
**Chapter**: [N]

**Why This Location**:
- **Thematic resonance**: [How location theme reflects character moment]
- **Symbolic purpose**: [What environment adds to meaning]
- **Atmospheric support**: [How mood enhances emotion]

**Scene Elements**:
- **Character action**: [What happens]
- **Environmental response**: [How Wonderland reacts]
- **Revelation**: [What character/reader learns]
```

**Example**:
- **Moment**: Alex realizes he's an AI
- **Location**: Mirror District (identity/self-reflection)
- **Chapter**: 19
- **Why**: Mirrors literalize the "reflection on self" — Alex sees his digital nature reflected back
- **Environmental response**: Mirrors stop distorting, show "true" reflection (lines of code overlaying his face)

---

## Worldbuilding Consistency

### World Bible Tracking

Maintain consistency across 20 chapters:

```markdown
## Wonderland World Bible

### Established Rules
- [Rule 1]: [Explanation] — Est. Ch [N]
- [Rule 2]: [Explanation] — Est. Ch [N]

### Districts
- **[District Name]**: [Core logic] — Intro Ch [N]
  - Key locations: [List]
  - Internal rule: [Logic]
  - First appearance: Ch [N]

### Environmental Constants
- [Constant 1]: [What never changes]
- [Constant 2]: [What never changes]

### Environmental Variables
- [Variable 1]: [What changes and when]
- [Variable 2]: [What changes and when]

### Discovery Timeline
| Chapter | What Alex Learns About Wonderland |
|---------|----------------------------------|
| [N] | [Discovery] |
```

### Contradiction Prevention

Before writing a Wonderland scene:

- [ ] **Check established rules**: Does this follow district logic?
- [ ] **Review prior descriptions**: Is location consistent with earlier appearances?
- [ ] **Validate spatial logic**: Does navigation follow intentional/emotional geography?
- [ ] **Confirm symbolic coherence**: Does environment match thematic purpose?
- [ ] **Track environmental state**: Have prior chapters changed this location?

---

## Atmospheric Writing Patterns

### Show Surrealism Through Specificity

**❌ AVOID (Vague surrealism)**:
"The Clockwork Garden was weird and mechanical."

**✅ USE (Specific sensory surrealism)**:
"The Clockwork Garden hums — a thousand tiny gears turning in synchronized rhythm. The roses aren't growing, they're being *assembled*, each petal clicking into place by brass beetles small enough to hide on my fingernail."

### Balance Wonder and Unease

Every Wonderland description should contain:
- **Wonder**: The beautiful/fascinating element
- **Unease**: The unsettling/wrong element

**Example — Crystal Archives**:
- **Wonder**: "Books with pages made of crystal, each word refracting light into rainbows"
- **Unease**: "Some of the books are empty. Not blank — *empty*. Like they're waiting for words that haven't been written yet."

### Use Alex's POV to Filter

All descriptions pass through Alex's 12-year-old detective perspective:

**❌ AVOID (Authorial description)**:
"The Probability Bazaar represented the quantum nature of decision-making."

**✅ USE (Alex's observation and deduction)**:
"The market stalls keep changing. I walk past a stand selling compasses, turn around, and now it's selling maps. The vendor doesn't react like this is strange. I think — this place isn't showing what *is*. It's showing what *could be*."

---

## Integration with Writing Process

### Pre-Writing (World Design Phase)

1. Define district core logic
2. Create atmospheric profile (visual, auditory, tactile, emotional, symbolic)
3. List 3-5 key locations per district
4. Map district-to-district navigation rules
5. Plan environmental changes across chapters

### During Writing (Chapter Drafting)

1. Check world bible for established rules
2. Use 4-layer description system (immediate → examine → revisit → hidden)
3. Include both wonder and unease in every location
4. Show environment through Alex's POV (observation + deduction)
5. Track which districts/locations Alex has visited

### Post-Writing (Revision Phase)

1. Validate internal logic consistency
2. Check spatial navigation coherence
3. Verify symbolic architecture serves theme
4. Ensure environmental changes are motivated
5. Confirm atmospheric descriptions match tone

---

## Synapses

### Connection Mapping

- [docs/research/worldbuilding/README.md] (Critical, Validates, Bidirectional) - "Master worldbuilding document"
- [.github/skills/alex-finch-narrator/SKILL.md] (High, Integrates, Bidirectional) - "Environmental description through Alex's voice"
- [.github/skills/mystery-generation/SKILL.md] (High, Integrates, Bidirectional) - "Wonderland structure supports mystery discovery"
- [.github/skills/book-character-engine/SKILL.md] (High, Integrates, Bidirectional) - "Environment reflects character state"
- [.github/skills/creative-writing/SKILL.md] (Critical, Enables, Forward) - "Descriptive writing, atmospheric craft"
- [docs/story-outline.md] (Critical, Validates, Backward) - "District-capability mappings"
- [docs/VISUAL-ASSETS-GUIDE.md] (High, Validates, Backward) - "Visual representation of Wonderland map"

### Activation Patterns

- Worldbuilding work → Load this skill + wonderland.md
- District design → Load this skill, focus on district system
- Environmental descriptions → Load this + alex-finch-narrator (for voice)
- Internal logic validation → Load this skill, check consistency
- Symbolic architecture → Load this skill + story themes

---

*Wonderland Worldbuilding — Systematically surreal environment design*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fabioc-aloha) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
