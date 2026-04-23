---
name: ux-mental-model-matcher
description: Evaluate if UI structure and terminology match user expectations and mental models. Use when assessing information architecture fit, identifying conceptual mismatches, evaluating terminology choices, or when user mentions "mental model", "user expectations", "makes sense", "intuitive", "confusing structure", or "where would users expect". Use when this capability is needed.
metadata:
  author: unthinkmedia
---

# UX Mental Model Matcher

Assess alignment between UI design and user mental models.

## What is a Mental Model?

A mental model is the user's internal representation of how something works. When UI matches mental models, interactions feel intuitive. When mismatched, users feel confused.

**Example mismatch:**
- User expects "Settings" to contain theme preferences
- App puts theme in "Appearance" under "Display"
- User can't find it → frustration

## Evaluation Dimensions

### 1. Information Architecture Fit
Does the structure match how users think about the domain?

**Check:**
- Category groupings make sense
- Items are where users expect them
- Parent-child relationships are logical
- Sibling items are truly related

### 2. Terminology Match
Do labels use words users would use?

**Check:**
- Domain terms vs. technical jargon
- Verb choices match user intent
- Consistent with industry standards
- Avoids internal company terms

### 3. Workflow Alignment
Does the sequence match user's mental process?

**Check:**
- Steps in expected order
- Prerequisites obvious
- No surprise requirements
- Exit points where expected

### 4. Conceptual Metaphors
Do visual/interaction metaphors match expectations?

**Check:**
- Icons represent expected concepts
- Spatial relationships match meaning
- Actions produce expected results
- Feedback matches mental model

## Assessment Process

### Step 1: Identify User Mental Model
What does the target user expect?

Questions to consider:
- What domain knowledge do they have?
- What similar products have they used?
- What terminology do they use?
- What workflow do they expect?

### Step 2: Map Current Design
Document what the design presents:
- Navigation structure
- Terminology used
- Workflow sequence
- Conceptual metaphors

### Step 3: Compare and Identify Gaps
Where do design and expectations diverge?

## Common Mismatch Patterns

### Organizational Mismatch
| User Expects | Design Shows | Issue |
|--------------|--------------|-------|
| By task | By feature | Can't find relevant features |
| Alphabetical | By importance | Scanning strategy fails |
| By frequency | By category | Common items buried |
| Flat list | Deep hierarchy | Over-navigation |

### Terminology Mismatch
| User Says | Design Says | Impact |
|-----------|-------------|--------|
| "Projects" | "Workspaces" | Search/scan failure |
| "Delete" | "Archive" | Behavior mismatch |
| "Settings" | "Preferences" | Minor confusion |
| "Save" | "Commit" | Domain confusion |

### Workflow Mismatch
| User Expects | Design Requires | Impact |
|--------------|-----------------|--------|
| Create then configure | Configure then create | Abandoned attempts |
| One-step action | Multi-step wizard | Impatience |
| Edit in place | Navigate to edit page | Extra steps |
| Immediate feedback | Delayed/batched | Uncertainty |

### Conceptual Mismatch
| User Model | Design Model | Impact |
|------------|--------------|--------|
| Folder hierarchy | Tag-based | Organization confusion |
| Own vs shared | All shared | Privacy concerns |
| Undo available | Permanent actions | Anxiety |
| Autosave | Manual save | Data loss risk |

## Output Format

    # Mental Model Assessment: [Feature/Flow Name]

    ## User Profile
    - **Role:** [Target user role]
    - **Domain expertise:** [Novice/Intermediate/Expert]
    - **Similar tools used:** [List]
    - **Expected terminology:** [Key terms they use]

    ## Alignment Analysis

    ### Information Architecture
    | Element | User Expectation | Current Design | Match? |
    |---------|-----------------|----------------|--------|
    | [Item] | [Expected location] | [Actual location] | ✅/❌ |

    ### Terminology
    | Concept | User's Term | Design's Term | Match? |
    |---------|-------------|---------------|--------|
    | [Concept] | [Term] | [Term] | ✅/❌ |

    ### Workflow
    | Step | Expected Sequence | Actual Sequence | Match? |
    |------|-------------------|-----------------|--------|
    | [Task] | [Expected order] | [Actual order] | ✅/❌ |

    ## Mismatch Impact

    | Mismatch | Severity | User Impact |
    |----------|----------|-------------|
    | [Issue] | High/Med/Low | [Consequence] |

    ## Recommendations

    ### High Priority (Mental model violation)
    1. **[Change]**: Aligns with user expectation of [X]

    ### Medium Priority (Learnable but friction)
    1. **[Change]**: Reduces initial confusion about [X]

    ### Low Priority (Minor mismatch)
    1. **[Change]**: Improves discoverability of [X]

## Interview Questions Template

To validate mental model assumptions:

1. "Where would you expect to find [feature]?"
2. "What would you call this type of [item]?"
3. "What would you expect to happen when you [action]?"
4. "Walk me through how you'd [accomplish task]"
5. "What similar tools have you used?"

## Domain-Specific Considerations

### Developer Tools
- Expect: Terminal/CLI familiarity
- Expect: Configuration-as-code models
- Watch for: Non-technical oversimplification

### Business Users
- Expect: Excel/spreadsheet mental models
- Expect: Familiar business terminology
- Watch for: Technical implementation exposure

### Consumer Apps
- Expect: Social media interaction patterns
- Expect: Mobile-first gestures
- Watch for: Desktop-centric assumptions

## References

For mental model research patterns: See [references/mental-model-research.md](references/mental-model-research.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/unthinkmedia) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
