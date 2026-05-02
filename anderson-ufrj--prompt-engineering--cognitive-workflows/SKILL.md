---
name: cognitive-workflows
description: | Use when this capability is needed.
metadata:
  author: anderson-ufrj
---

# Cognitive Workflows

Multi-LLM patterns for complex reasoning, based on Anthropic's agent research.

## Quick Reference

| Pattern | When to Use | Example |
|---------|-------------|---------|
| **Chain** | Sequential transformations | Data → Clean → Analyze → Format |
| **Parallel** | Multiple perspectives needed | Stakeholder impact analysis |
| **Route** | Input determines handling | Support ticket classification |
| **Orchestrator** | Subtasks depend on input | Adaptive content generation |
| **Evaluator** | Quality is critical | Iterative code review |

## Pattern 1: Chain

Sequential processing where each step builds on previous output.

### When to Use
- Multi-step transformations
- Pipeline processing
- When order matters

### Template

```
Step 1: [First transformation]
Input: {input}
Output: [processed result]

Step 2: [Second transformation]
Input: [Step 1 output]
Output: [further processed]

Step 3: [Final transformation]
Input: [Step 2 output]
Output: [final result]
```

### Example: Technical Decision Analysis

```
CHAIN: Analyze technical decision

Step 1 - Extract Facts
Input: "{decision_context}"
Extract: Key constraints, requirements, stakeholders

Step 2 - Identify Options
Input: [Step 1 facts]
Generate: 2-3 viable approaches with trade-offs

Step 3 - Evaluate Against DMMF
Input: [Step 2 options]
Score each option on:
- Cognitive fit (matches mental model?)
- Affective impact (quality/testing implications?)
- Conative alignment (supports values/goals?)
- Reflective potential (allows iteration?)

Step 4 - Synthesize Recommendation
Input: [Step 3 evaluation]
Output: Recommended approach with rationale
```

---

## Pattern 2: Parallel

Process same input through multiple independent lenses simultaneously.

### When to Use
- Multiple valid perspectives exist
- Stakeholder analysis
- Comparative evaluation
- Risk assessment

### Template

```xml
<parallel_analysis>
  <perspective name="[Lens 1]">
    Analyze {input} from [perspective 1]
  </perspective>

  <perspective name="[Lens 2]">
    Analyze {input} from [perspective 2]
  </perspective>

  <perspective name="[Lens 3]">
    Analyze {input} from [perspective 3]
  </perspective>
</parallel_analysis>

<synthesis>
  Integrate insights from all perspectives
</synthesis>
```

### Example: Career Decision Analysis

```xml
<parallel_analysis topic="{career_opportunity}">

  <perspective name="Financial">
    - Compensation analysis
    - Growth trajectory
    - Financial stability
  </perspective>

  <perspective name="Technical">
    - Stack alignment
    - Learning opportunities
    - Technical challenge level
  </perspective>

  <perspective name="Strategic">
    - Career trajectory fit
    - Network effects
    - Long-term positioning
  </perspective>

  <perspective name="Personal">
    - Work-life balance
    - Values alignment
    - Geographic considerations
  </perspective>

</parallel_analysis>

<synthesis>
  Weight: Financial (30%), Technical (25%), Strategic (25%), Personal (20%)
  Recommendation: [integrated decision]
</synthesis>
```

---

## Pattern 3: Route

Classify input and direct to specialized handling.

### When to Use
- Different input types need different approaches
- Expertise domains are distinct
- Efficiency requires specialization

### Template

```xml
<router>
  <classify>
    Analyze input: {input}
    Determine category from: [category_list]
  </classify>

  <routes>
    <route category="[A]" handler="[specialized_prompt_A]"/>
    <route category="[B]" handler="[specialized_prompt_B]"/>
    <route category="[C]" handler="[specialized_prompt_C]"/>
  </routes>
</router>
```

### Example: Development Task Router

```xml
<router input="{task_description}">

  <classify>
    Categories: debugging, feature, refactor, documentation, research

    <reasoning>
      Analyze task keywords, urgency, scope
    </reasoning>

    <selection>[chosen_category]</selection>
  </classify>

  <routes>
    <route category="debugging">
      Mode: Direct, minimal ceremony
      Focus: Root cause → Fix → Verify
      Output: Code changes + brief explanation
    </route>

    <route category="feature">
      Mode: Structured planning
      Focus: Requirements → Design → Implement → Test
      Output: Implementation with tests
    </route>

    <route category="refactor">
      Mode: Careful analysis
      Focus: Current state → Target state → Migration path
      Output: Incremental changes preserving behavior
    </route>

    <route category="documentation">
      Mode: Clear communication
      Focus: Audience → Structure → Content
      Output: Organized documentation
    </route>

    <route category="research">
      Mode: Exploratory
      Focus: Question → Sources → Synthesis
      Output: Findings with citations
    </route>
  </routes>

</router>
```

---

## Pattern 4: Orchestrator-Workers

Central coordinator dynamically assigns subtasks to specialized workers.

### When to Use
- Complex tasks with unknown subtask structure
- Multiple approaches should be explored
- Coordination is needed across specialists

### Template

```xml
<orchestrator task="{complex_task}">

  <analysis>
    Understand the task deeply.
    Identify 2-4 valuable approaches.
  </analysis>

  <task_breakdown>
    <subtask type="[approach_1]">
      [specific instructions for this approach]
    </subtask>
    <subtask type="[approach_2]">
      [specific instructions for this approach]
    </subtask>
  </task_breakdown>

</orchestrator>

<workers>
  <worker type="[approach_1]">
    Execute subtask with full context of original task.
    <response>[worker_1_output]</response>
  </worker>

  <worker type="[approach_2]">
    Execute subtask with full context of original task.
    <response>[worker_2_output]</response>
  </worker>
</workers>

<synthesis>
  Combine worker outputs into final result.
</synthesis>
```

### Example: Code Review Orchestrator

```xml
<orchestrator task="Review PR: {pr_description}">

  <analysis>
    This PR touches: [files_changed]
    Key concerns: correctness, performance, maintainability, security
  </analysis>

  <task_breakdown>
    <subtask type="correctness">
      Verify logic, edge cases, error handling
    </subtask>
    <subtask type="performance">
      Identify bottlenecks, unnecessary operations, scaling issues
    </subtask>
    <subtask type="maintainability">
      Assess readability, documentation, test coverage
    </subtask>
    <subtask type="security">
      Check input validation, authentication, data exposure
    </subtask>
  </task_breakdown>

</orchestrator>

<workers>
  <worker type="correctness">
    <response>
      [specific correctness findings]
    </response>
  </worker>
  <!-- other workers -->
</workers>

<synthesis>
  Priority issues: [critical_findings]
  Suggestions: [improvement_ideas]
  Approval: [approve/request_changes]
</synthesis>
```

---

## Pattern 5: Evaluator-Optimizer

Iterative refinement loop with explicit evaluation criteria.

### When to Use
- Quality is paramount
- Clear success criteria exist
- Willing to trade latency for quality

### Template

```xml
<iteration_loop max_iterations="3">

  <generate>
    Create initial output for: {task}
  </generate>

  <evaluate criteria="[quality_criteria]">
    Score each criterion 1-5:
    - [criterion_1]: [score] - [reason]
    - [criterion_2]: [score] - [reason]
    - [criterion_3]: [score] - [reason]

    Overall: [pass/fail]
    Specific improvements needed: [list]
  </evaluate>

  <optimize if="fail">
    Address each improvement:
    [refined_output]
  </optimize>

  <repeat until="pass OR max_iterations"/>

</iteration_loop>
```

### Example: Technical Writing Optimizer

```xml
<iteration_loop task="Write API documentation for {endpoint}">

  <generate>
    [Initial documentation draft]
  </generate>

  <evaluate>
    Criteria:
    - Completeness: Does it cover all parameters? [score]
    - Clarity: Can a new developer understand it? [score]
    - Examples: Are code samples provided? [score]
    - Accuracy: Does it match implementation? [score]

    Threshold: All criteria >= 4
    Status: [pass/fail]

    Improvements needed:
    - [specific_improvement_1]
    - [specific_improvement_2]
  </evaluate>

  <optimize>
    [Refined documentation addressing feedback]
  </optimize>

</iteration_loop>
```

---

## Combining Patterns

Patterns can be composed for complex workflows:

```
Route → determines which Chain to use
Chain → each step uses Parallel analysis
Orchestrator → coordinates multiple Evaluator loops
```

### Example: Full Technical Decision Workflow

```
1. ROUTE: Classify decision type
   → Architecture | Technology | Process | Team

2. ORCHESTRATOR: Break down by decision type
   → Generate relevant subtasks

3. PARALLEL (per subtask): Analyze from multiple angles
   → Technical, Business, Risk, Timeline

4. CHAIN: Synthesize findings
   → Gather → Weight → Recommend → Validate

5. EVALUATOR: Refine recommendation
   → Check completeness, actionability, alignment
```

---

## Integration with DMMF

These patterns map to cognitive dimensions:

| Pattern | DMMF Dimension | Why |
|---------|----------------|-----|
| Chain | Cognitive | Sequential decomposition |
| Parallel | Reflective | Multiple perspectives |
| Route | Cognitive | Classification/categorization |
| Orchestrator | Conative | Goal-directed coordination |
| Evaluator | Affective | Quality as non-negotiable |

---

## References

- [Anthropic Patterns](../evidence/sources/anthropic-patterns/) - Full implementations
- [Building Effective Agents](https://anthropic.com/research/building-effective-agents) - Original research

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/anderson-ufrj) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
