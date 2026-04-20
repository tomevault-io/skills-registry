---
name: edu-demo-orchestrator
description: | Use when this capability is needed.
metadata:
  author: hanialshater
---

# Educational Demo Orchestrator

Orchestrate the full cycle: Test Cases → Build → Evaluate (Free + Tests) → Iterate

## Evolution Workflow

```
┌─────────────────────────────────────────────────────────────┐
│  PHASE 0: TEST CASE DEVELOPMENT (once per problem)          │
│  └─ Skill: edu-demo-test-cases                              │
│  └─ Output: test_cases.json (learning verification tests)   │
├─────────────────────────────────────────────────────────────┤
│  PHASE 1: BUILD (each generation)                           │
│  └─ Skill: edu-demo-builder (5 agents, diverse approaches)  │
│  └─ Output: generations/genN/agent_1.html ... agent_5.html  │
├─────────────────────────────────────────────────────────────┤
│  PHASE 2a: FREE EVALUATION (Agent A - BLIND)                │
│  └─ Watch demo like learner (no test cases, no benchmark)   │
│  └─ Output: agent_X_free_eval.json (honest assessment)      │
├─────────────────────────────────────────────────────────────┤
│  PHASE 2b: TEST CASE RUNNER (Agent B - SYSTEMATIC)          │
│  └─ Execute test_cases.json against each agent              │
│  └─ Output: agent_X_tests.json (PASS/FAIL results)          │
├─────────────────────────────────────────────────────────────┤
│  PHASE 3: ORCHESTRATE (You - COMPLETE VIEW)                 │
│  └─ Combine free eval + test results + benchmark reference  │
│  └─ Decide: winners, what to fix, what to combine for gen2  │
└─────────────────────────────────────────────────────────────┘
```

## Core Principle: Educational Value First

**At EVERY generation, evaluate and optimize for:**

1. **Learning Outcome** - Does the student understand the concept?
   - Can they explain why the algorithm works?
   - Do they see the key insight?
   - Would they remember this in a week?

2. **Clarity** - Is the visualization unambiguous?
   - Are algorithm states clear?
   - Can a beginner follow the steps?
   - Is visual feedback obvious?

3. **Engagement** - Does it hold attention and interest?
   - Would a learner want to explore further?
   - Are explanations compelling or boring?
   - Does it spark curiosity?

4. **Accessibility** - Can diverse learners succeed?
   - Works for visual learners? Logical learners? Kinesthetic learners?
   - No assumptions about prior knowledge?
   - Clear metaphors that don't confuse?

5. **Correctness** - Are test cases passing?
   - Algorithm implementation correct?
   - Edge cases handled?
   - No bugs or crashes?

**Remember**: Passing all 15 test cases ≠ good education. A technically perfect demo can still fail to teach.

---

## Screenshot System (Built-in)

All demos include automatic screenshot capture via html2canvas:

**For builders:**
- Base.html includes `window.screenshotManager` API
- Call `window.screenshotManager.captureState('label')` after key algorithm steps
- This lets evaluators capture learning moments

**For evaluators:**
- Demos have "📸 Capture State" button to manually capture key moments
- Demos have "⬇️ Download Screenshots" button to download all as PNGs
- No need to fiddle with file saving - evaluators just click buttons

**For orchestrator:**
- Evaluators document what they learned, not screenshot filenames
- Screenshots are supporting evidence, not the evaluation itself
- Focus on findings (bugs, clarity, learning outcomes) in JSON output

---

## Phase 0: Develop Test Cases (FIRST)

Before ANY building, ensure test cases exist:

```
# Check if test cases exist
Read(problems/<name>/test_cases.json)

# If not, spawn test case developer
Task(
  subagent_type='general-purpose',
  prompt='''
  Skill: edu-demo-test-cases

  Problem: problems/<name>/
  Read problem.md to understand the concept.

  Develop test cases that verify LEARNING outcomes:
  - What operations must the demo teach?
  - What should a learner SEE and UNDERSTAND?
  - What edge cases test comprehension?

  Output: problems/<name>/test_cases.json
  '''
)
```

**Test cases are developed ONCE per problem**, not per generation.

## Orchestrator Operations Vocabulary

These operations define your strategy for each generation. Assign them to agents—they execute, they don't choose.

**Enhancement Operations:**
- `crossover` - Blend ideas from 2+ predecessor agents (study their screenshots and code)
- `add_sophistication` - Deepen visual complexity, add detail layers, enhance interactivity
- `simplify` - Remove non-essential elements, reduce cognitive load, strip to core concept
- `refine_details` - Polish UI/UX, fix spacing, improve typography, enhance visual hierarchy
- `fix_bugs` - Repair issues found in evaluation (missing test cases, interaction bugs, etc.)

**Design Operations:**
- `iterate_patterns` - Try new visual metaphors (different from all predecessors)
- `improve_pedagogy` - Enhance learning effectiveness (clearer explanations, better pacing)
- `accessibility_enhance` - Optimize for diverse learners (visual, logical, kinesthetic)
- `performance_optimize` - Make it faster, smoother, more responsive

**Advanced Operations:**
- `variation_explore` - Focus on high-variance dimensions (colors, layout, interaction style)
- `edge_case_handling` - Explicitly test and handle edge cases
- `learner_testing` - Conduct real user testing with learners

**How to Use:**
```
Task(prompt='''
  Skill: edu-demo-builder

  Generation: 2
  Vibe: comparison (improve narrative approach)
  Operations: [fix_bugs, refine_details, add_sophistication]

  Output: gen2/agent_1.html
''')
```

Agents receive operations as assignments, not choices.

## Orchestrator Strategies

Choose strategy based on generation context:

**1. Hyperband Strategy** (if early generation shows promise)
```
IF gen_N small variants look good:
  → Scale to bigger implementation in gen_{N+1}
  → Try more ambitious visual design
  → Add more interactive features
```

**2. CMA-ES Strategy** (focus on high-variance dimensions)
```
IF dimensions have high variance across agents:
  → Build gen_{N+1} specifically exploring those dimensions
  → Example: If layout varies widely (2-panel, 3-panel, full-screen)
  → Try 4-panel, minimal, nested, etc.
```

**3. Evolutionary Strategies** (population-based)
```
EVERY generation:
  → Keep top 3 performers from gen_N
  → Mutate each with different operations
  → Measure improvement
  → Select winners for gen_{N+1}
```

**4. Trust Region Strategy** (local refinement)
```
IF strong winner found in gen_N:
  → Build gen_{N+1} as variants of winner
  → Apply: fix_bugs, refine_details, add_sophistication
  → Keep core concept identical, refine execution
```

**5. Adaptive Bandit Strategy** (explore vs exploit)
```
Early gens (1-2): EXPLORE - diverse approaches
Mid gens (3-4):   EXPLOIT - build on winners
Late gens (5+):   REFINE - polish best solutions
```

## Generation Planning: Spawn Size & Pruning

### Generation Progression

```
GEN 1 (EXPLORE):  5 agents - diverse approaches
                  → Evaluate all 5
                  → Prune to top 3 winners
                  → Document lessons learned

GEN 2 (BUILD):    4-5 agents - improve winners + new ideas
                  → Agent 1-3: improve top gen1 winners
                  → Agent 4-5: explore new directions
                  → Evaluate all
                  → Prune to top 2-3 winners

GEN 3 (REFINE):   3 agents - refine winners
                  → All 3 are variants of best approaches
                  → Different refinement operations
                  → Evaluate all
                  → Select best 1-2 for gen 4+

GEN 4+ (POLISH):  1-2 agents - final refinement
                  → Merge best features
                  → Polish to production quality
```

### Pruning Criteria

After evaluation, rank by:
1. Learning outcome (primary metric)
2. Test case passing rate
3. Interaction clarity
4. Visual design quality
5. Technical stability

Keep only agents scoring > median on multiple criteria.

## Phase 1: Spawn Builders (Weak Orchestration)

You assign direction + operations. Builders discover their approach.

**Key principle: Be explicit about STUDY references**
- Don't say "study /screenshots/ folder" - builders explore everything
- DO say "Study agent_4.html and its screenshots" - focused context
- Orchestrator makes strategic decisions; builders execute discovery

Builders will:
1. Copy base.html first (default starting point)
2. Study ONLY the specific references you provide (code + screenshots)
3. Read LESSONS_LEARNED.md for overall context
4. Decide: patch a winner, or start fresh
5. Apply operations (your strategy vocabulary)
6. Document their discovery (approach.md)

Don't prescribe patches or methods—let builders discover within boundaries.

## Bootstrap: Copy Benchmark_UX Screenshots

When creating a NEW problem directory, copy benchmark_ux reference screenshots from central location:

```bash
# Central source: /Users/hani/code-evo-agent-simple/bench/benchmark_ux/

# Copy relevant benchmark screenshots to problem directory:
cp /bench/benchmark_ux/05_arrays_subarrays_delete.png /problems/<name>/benchmark_ux/
cp /bench/benchmark_ux/<other_relevant>.png /problems/<name>/benchmark_ux/
```

**Why:** Evaluators need benchmark reference screenshots to assess visual quality and clarity.

**When:** During initial problem setup (before Gen 1 builders spawn).

**Selection:** Choose screenshots that represent HIGH QUALITY examples of similar concepts:
- Array visualizations for sorting algorithms
- Tree visualizations for tree algorithms
- Graph visualizations for graph algorithms
- etc.

Builders will see base.html structure. Evaluators will see both agent implementations AND benchmark references to compare against.

---

Builders in parallel (size depends on strategy & generation):

```python
# GEN 1 EXAMPLE (EXPLORE - 5 diverse vibes)
# Builders copy base first, then discover their approach

Task(prompt='''
  Skill: edu-demo-builder

  Generation: 1
  Vibe: narrative storytelling
  Operations: [improve_pedagogy, add_sophistication]

  START: Copy base.html to gen1/agent_1.html

  DISCOVER:
  - Tell a story around the algorithm
  - What narrative hooks engagement?
  - How does storytelling teach the concept?

  OUTPUT:
  - gen1/agent_1.html
  - agent_1_approach.md (explain your vibe)
''')

Task(prompt='''
  Skill: edu-demo-builder

  Generation: 1
  Vibe: minimalist zen
  Operations: [simplify, refine_details]

  START: Copy base.html to gen1/agent_2.html

  DISCOVER:
  - Strip to absolute essentials
  - What is the core concept without noise?
  - Can clarity come from simplicity?

  OUTPUT:
  - gen1/agent_2.html
  - agent_2_approach.md
''')

Task(prompt='''
  Skill: edu-demo-builder

  Generation: 1
  Vibe: interactive/hands-on
  Operations: [iterate_patterns, add_sophistication]

  START: Copy base.html to gen1/agent_3.html

  DISCOVER:
  - Make learner actively do the algorithm
  - What interactions deepen understanding?
  - Can hands-on discovery beat passive watching?

  OUTPUT:
  - gen1/agent_3.html
  - agent_3_approach.md
''')

Task(prompt='''
  Skill: edu-demo-builder

  Generation: 1
  Vibe: comparison/dual-view
  Operations: [improve_pedagogy, performance_optimize]

  START: Copy base.html to gen1/agent_4.html

  DISCOVER:
  - Show two approaches side-by-side
  - Why is comparison powerful for teaching?
  - What metrics make the difference visible?

  OUTPUT:
  - gen1/agent_4.html
  - agent_4_approach.md
''')

Task(prompt='''
  Skill: edu-demo-builder

  Generation: 1
  Vibe: game/challenge
  Operations: [iterate_patterns, accessibility_enhance]

  START: Copy base.html to gen1/agent_5.html

  DISCOVER:
  - Gamify the algorithm learning
  - What challenges engage learners?
  - Can play teach understanding?

  OUTPUT:
  - gen1/agent_5.html
  - agent_5_approach.md
''')

# GEN 2 EXAMPLE (BUILD on winners - 4 agents)
# Read gen1/LESSONS_LEARNED.md to understand what worked
# Winners: agent_1 (narrative - 87), agent_4 (comparison - 92)

Task(prompt='''
  Skill: edu-demo-builder

  Generation: 2
  Vibe: narrative (improve from gen1)
  Operations: [fix_bugs, refine_details, add_sophistication]

  STUDY (ONLY these references):
  - /generations/gen1/agent_1.html (scored 87)
  - /screenshots/agent_1_*.png (visualize the narrative approach)
  - /LESSONS_LEARNED.md (why narrative worked)

  DISCOVER:
  - How to apply operations to improve narrative vibe?
  - Patch agent_1, or start fresh?
  - What makes narrative effective for this concept?

  OUTPUT:
  - gen2/agent_1.html
  - agent_1_approach.md (explain your strategy)
''')

Task(prompt='''
  Skill: edu-demo-builder

  Generation: 2
  Vibe: comparison (improve from gen1)
  Operations: [crossover, fix_bugs, performance_optimize]

  STUDY (ONLY these references):
  - /generations/gen1/agent_4.html (scored 92 - best gen1)
  - /screenshots/agent_4_*.png (visualize the comparison approach)
  - /generations/gen1/agent_1.html (narrative ideas to blend in)
  - /screenshots/agent_1_*.png
  - /LESSONS_LEARNED.md

  DISCOVER:
  - How to build on comparison approach?
  - What from narrative vibe could enhance it?
  - How to optimize performance further?

  OUTPUT:
  - gen2/agent_2.html
  - agent_2_approach.md
''')

Task(prompt='''
  Skill: edu-demo-builder

  Generation: 2
  Vibe: hybrid (blend narrative + comparison)
  Operations: [crossover, add_sophistication, refine_details]

  STUDY (ONLY these references):
  - /generations/gen1/agent_1.html (narrative - 87)
  - /screenshots/agent_1_*.png
  - /generations/gen1/agent_4.html (comparison - 92)
  - /screenshots/agent_4_*.png
  - /LESSONS_LEARNED.md (why each worked)

  DISCOVER:
  - How to blend these two vibes effectively?
  - Which strengths from each complement each other?
  - Patch one with ideas from the other, or fresh hybrid?

  OUTPUT:
  - gen2/agent_3.html
  - agent_3_approach.md
''')

Task(prompt='''
  Skill: edu-demo-builder

  Generation: 2
  Vibe: new direction
  Operations: [iterate_patterns, improve_pedagogy]

  STUDY (ONLY these references):
  - /LESSONS_LEARNED.md (what vibes resonated, why)
  - /screenshots/ folder: Study only HIGH-VARIANCE dimensions
    (e.g., if layouts differ widely, study those differences)

  DISCOVER:
  - What unexplored vibe could work?
  - Focus on teaching effectiveness over visual novelty
  - What dimension had the most variance? Explore beyond it.

  OUTPUT:
  - gen2/agent_4.html
  - agent_4_approach.md
''')
```

### Screenshot Capture During Evaluation

Evaluators must save screenshots:
- Initial state
- After key interactions
- Final state
- Error conditions
- Save to: `generations/genN/screenshots/agent_X_step_1.png`, etc.

# All agents are BLIND to benchmark
# All agents run behind HTTP server

## Phase 2a: Free Evaluation (Agent A - BLIND)

**Watch demo like a learner. No test cases bias. No benchmark leakage.**

```python
Task(
  subagent_type='general-purpose',
  prompt='''
  Skill: edu-demo-evaluator-free

  Demo: http://localhost:9999/problems/<name>/generations/gen1/agent_1.html

  Watch like a learner would:
  - First impression?
  - Does it make sense?
  - Is it engaging or confusing?
  - What's awesome? What's weak?
  - Would a learner understand the concept?

  DO NOT:
  - Think about test cases
  - Compare to any benchmark
  - Use scoring rubric

  OUTPUT: agent_1_free_eval.json
  {
    "agent": "gen1/agent_1",
    "impression": "...",
    "what_works": [...],
    "what_doesn't_work": [...],
    "learner_impact": "...",
    "recommendation": "..."
  }
  '''
)
# Repeat for agents 2-5
```

## Phase 2b: Test Case Runner (Agent B - SYSTEMATIC)

**Execute test_cases.json. Report PASS/FAIL. Find bugs.**

```python
Task(
  subagent_type='general-purpose',
  prompt='''
  Skill: edu-demo-evaluator-tests

  Demo: http://localhost:9999/problems/<name>/generations/gen1/agent_1.html
  Test cases: problems/<name>/test_cases.json

  Setup:
  - Start HTTP server if needed
  - Get Chrome tab (reuse for sequential agents)
  - Read test_cases.json

  For each agent (agent_1 through agent_5):
    1. Navigate to http://localhost:9999/problems/<name>/generations/gen{N}/agent_X.html
    2. Execute each test case step by step
    3. Screenshot after key steps
    4. Report: PASS/FAIL/PARTIAL
    5. Save: agent_X_tests.json

  OUTPUT: agent_1_tests.json, agent_2_tests.json, etc.
  {
    "agent": "gen1/agent_1",
    "test_results": [
      {"id": "pivot_visibility", "result": "PASS"},
      {"id": "comparison_counter", "result": "PASS"},
      ...
    ],
    "bugs_found": [],
    "blockers": "None"
  }
  '''
)
```

## Phase 3: Orchestrate (You Make Decisions)

You have three data sources now:

**Data Source 1: Free Evaluations (agent_X_free_eval.json)**
- What's awesome? What's weak?
- Learner impact
- Honest assessment (no rubric bias)

**Data Source 2: Test Results (agent_X_tests.json)**
- Which tests PASS/FAIL?
- Any bugs blocking learning?
- Edge case failures?

**Data Source 3: Benchmark (benchmark_ux/*)**
- Quality reference (not judge)
- How does visual design compare?
- Educational approach similarity?

### Decision Framework

```
IF free_eval says "brilliant" AND tests all pass:
  → STRONG WINNER - build gen2 on this
  → Maybe add ideas from other agents

IF free_eval says "good concept" AND tests mostly pass:
  → FIX THE BUGS - gen2 version with fixes
  → Then evaluate again

IF tests fail:
  → BLOCKERS - demo doesn't teach correctly
  → Fix or abandon this approach

IF multiple good candidates:
  → COMBINE THEM - take awesome from agent_1 + awesome from agent_4
  → Build gen2 hybrid
```

### Example Decision (From Quicksort)

```
agent_1 (Narrative):
  Free eval: "Great storytelling, learners engage, but get lost in details"
  Tests: 14/15 pass, step_explanation PARTIAL
  Decision: Good concept, keep narrative style

agent_4 (Comparison):
  Free eval: "BRILLIANT - teaches concept immediately via comparison"
  Tests: 15/15 pass, all strong
  Decision: WINNER - build gen2 on this

FOR GEN2:
  Build agent_1: agent_4 foundation + agent_1's narrative richness
  Build agent_2: agent_1 narrative + agent_4's performance insights
  Build agent_3: agent_5 interactive + traditional watch mode
```

### Document Lessons Learned

After each generation evaluation, create/update:

```markdown
# LESSONS_LEARNED.md

## Generation 1 Results

**Winners:**
- agent_4 (comparison): 15/15 tests, 92/100 learning score
- agent_1 (narrative): 14/15 tests, 87/100 learning score

**Losers:**
- agent_5 (interactive): 11/15 tests, confusing interaction model

**Key Insights:**
1. Comparison view (side-by-side before/after) teaches fastest
2. Narrative framing helps but shouldn't override algorithm visibility
3. Interaction complexity (agent_5) hurt learning, not helped

**For Gen 2:**
- Build on agent_4's comparison approach
- Add agent_1's narrative descriptions
- Simplify interaction (agent_5 was too complex)
- Focus on: clarity > features
```

This file documents what worked, what didn't, and why.
For Gen 2 builders, reading LESSONS_LEARNED.md helps them understand context.

## Complete Example Session

```python
# === PHASE 0: Test Cases ===
# Check if exists
Read(problems/heap-demo/test_cases.json)  # Not found

# Develop test cases
Task(prompt='Skill: edu-demo-test-cases\nProblem: heap-demo\n...')
# Output: test_cases.json with insert, extract, edge case tests

# === PHASE 1: Build Gen 1 ===
# Spawn 5 diverse builders
Task(prompt='...storytelling approach...')  # agent_1
Task(prompt='...game-like approach...')     # agent_2
Task(prompt='...minimalist approach...')    # agent_3
Task(prompt='...comparison approach...')    # agent_4
Task(prompt='...real-world approach...')    # agent_5

# === PHASE 2: E2E Evaluate ===
# Evaluate each with Chrome tools
Task(prompt='Skill: edu-demo-evaluator\nDemo: agent_1.html\n...')
# Returns: {total: 45, test_cases: [{id: "insert", result: "PASS"}, ...]}

Task(prompt='Skill: edu-demo-evaluator\nDemo: agent_2.html\n...')
# Returns: {total: 62, test_cases: [...]}

# ... evaluate all 5

# === PHASE 3: Analyze & Iterate ===
# Results:
# agent_1: 45 (insert fails)
# agent_2: 62 (all pass, needs polish)
# agent_3: 38 (viewport issues)
# agent_4: 71 (best!)
# agent_5: 55 (extract fails)

# Decision: Build on agent_4, try to fix agent_2 and agent_5
# Gen 2: Polish agent_4, fix agent_2, fix agent_5, combine 4+2
```

## Test Case Maintenance

Test cases may need updates if:
- Problem definition changes
- New edge cases discovered
- Learning objectives clarified

```python
# Update test cases
Task(prompt='''
  Skill: edu-demo-test-cases

  Problem: heap-demo
  Existing: problems/heap-demo/test_cases.json

  ADD test case for:
  - Inserting duplicate values
  - Heap with single element

  Keep existing test cases, add new ones.
''')
```

## Directory Structure

```
problems/<name>/
├── problem.md              # Concept to teach
├── test_cases.json         # NEW: Learning verification tests
├── benchmark_ux/           # Quality reference
├── screenshots/            # E2E test screenshots
├── generations/
│   ├── gen1/
│   │   ├── agent_1.html
│   │   ├── agent_1_eval.json  # Evaluation results
│   │   └── ...
│   └── gen2/
└── LESSONS_LEARNED.md
```

## Key Principles

1. **Test cases first** - Know what to verify before building
2. **Chrome E2E testing** - Real browser interaction, not mocked
3. **Learning outcomes** - Verify the demo TEACHES correctly
4. **Iterate on failures** - Fix bugs before discarding good ideas
5. **Quality vs benchmark** - Score relative to reference quality

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hanialshater) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
