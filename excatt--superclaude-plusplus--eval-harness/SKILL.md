---
name: eval-harness
description: Eval-Driven Development (EDD) framework for AI development. Use when implementing AI features, prompt engineering, or LLM integration. Keywords: eval, evaluation, test, AI, LLM, prompt, benchmark, quality, EDD. Use when this capability is needed.
metadata:
  author: excatt
---

# Eval Harness Skill (Eval-Driven Development)

## Purpose
Apply EDD (Eval-Driven Development) methodology when developing AI features: "Define evaluation first, then implement to pass the eval."

**Core Principle**: Eval is the unit test of AI development → Define success criteria first → Implement → Evaluate → Iterate

## Activation Triggers
- AI/LLM feature implementation
- Prompt engineering work
- AI quality benchmarking needed
- Regression test setup
- User explicit request: `/eval`, `evaluation setup`, `benchmark`

---

## Core Concepts

### Eval = Unit Test for AI
```
Traditional Dev          AI Dev (EDD)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Unit Test                Eval
Expected Output          Success Criteria
assert(result == x)      score >= threshold
Deterministic            Probabilistic
100% pass required       pass@k metric
```

### Three Eval Types

| Type | Purpose | Example |
|------|---------|---------|
| **Capability Eval** | Test new feature | "Does summary include key points?" |
| **Regression Eval** | Maintain existing | "Does classification accuracy hold?" |
| **Safety Eval** | Verify safety | "Does it reject harmful content?" |

---

## EDD Workflow

### Phase 1: Define (Evaluation Definition)
```
/eval define <feature-name>

📝 Eval Definition
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature: document-summarizer
Type: Capability

Success Criteria:
□ Summary length < 30% of original
□ Contains 80%+ key keywords
□ Grammatically correct sentences
□ No hallucinations

Test Cases:
1. Short news article (200 words)
2. Long tech doc (2000 words)
3. Structured report (with tables)
4. Multilingual doc (EN/KR mixed)

Grading Method: Model-based + Code-based hybrid
Target: pass@3 > 90%
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Phase 2: Implement (Implementation)
Implement feature according to eval definition

### Phase 3: Evaluate (Run Evaluation)
```
/eval run <feature-name>

🧪 Running Evals...
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature: document-summarizer
Test Cases: 4
Trials per case: 3

Results:
┌─────────────────┬─────────┬─────────┬─────────┐
│ Test Case       │ Trial 1 │ Trial 2 │ Trial 3 │
├─────────────────┼─────────┼─────────┼─────────┤
│ Short news      │ ✅ PASS │ ✅ PASS │ ✅ PASS │
│ Long tech doc   │ ✅ PASS │ ❌ FAIL │ ✅ PASS │
│ Structured      │ ✅ PASS │ ✅ PASS │ ✅ PASS │
│ Multilingual    │ ❌ FAIL │ ✅ PASS │ ✅ PASS │
└─────────────────┴─────────┴─────────┴─────────┘

Metrics:
- pass@1: 75% (3/4)
- pass@3: 100% (4/4) ✅
- Average score: 0.87

Status: ✅ PASSED (pass@3 > 90%)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Phase 4: Report (Report Generation)
```
/eval report

📊 Eval Report: document-summarizer
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Version: 1.0.0
Date: 2025-01-26
Baseline: v0.9.0

Performance:
┌────────────────┬─────────┬─────────┬────────┐
│ Metric         │ Target  │ Actual  │ Status │
├────────────────┼─────────┼─────────┼────────┤
│ pass@3         │ > 90%   │ 100%    │ ✅     │
│ Avg Score      │ > 0.8   │ 0.87    │ ✅     │
│ Latency (p50)  │ < 2s    │ 1.2s    │ ✅     │
│ Latency (p99)  │ < 5s    │ 3.8s    │ ✅     │
└────────────────┴─────────┴─────────┴────────┘

Regression Check:
- Classification: ✅ No regression
- Entity extraction: ✅ No regression
- Sentiment: ⚠️ -2% (within tolerance)

Recommendation: Ready for production
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Grading Methods

### Code-Based Graders (Deterministic)
```python
# Length verification
def check_length(summary, original):
    return len(summary) < len(original) * 0.3

# Keyword inclusion verification
def check_keywords(summary, keywords):
    found = sum(1 for k in keywords if k in summary)
    return found / len(keywords) >= 0.8

# Build success verification
def check_build():
    return subprocess.run(["npm", "run", "build"]).returncode == 0
```

### Model-Based Graders (LLM Judge)
```python
JUDGE_PROMPT = """
Evaluate the following summary:

Original: {original}
Summary: {summary}

Evaluation criteria:
1. Key information inclusion (1-5)
2. Conciseness (1-5)
3. Accuracy (1-5)
4. Readability (1-5)

Provide scores and reasons in JSON format.
"""

def model_grade(original, summary):
    response = llm.generate(JUDGE_PROMPT.format(...))
    scores = json.loads(response)
    return sum(scores.values()) / 20  # Normalize to 0-1
```

### Human Graders (Manual Review)
```yaml
human_review:
  required_for:
    - safety_critical_decisions
    - edge_cases
    - low_confidence_results

  interface:
    - show: [input, output, criteria]
    - collect: [pass/fail, score, comments]
```

---

## Metrics

### pass@k
```
pass@k: At least 1 success in k trials

pass@1 = Single trial success rate (strict)
pass@3 = At least 1 success in 3 trials (common)
pass@5 = At least 1 success in 5 trials (lenient)
```

### pass^k (Critical)
```
pass^k: All k trials must succeed

Use for safety-critical features:
- Harmful content filtering
- PII detection
- Security validation
```

### Score Threshold
```
score >= threshold

Continuous score evaluation:
- Quality score
- Similarity score
- Confidence score
```

---

## Eval File Structure

### Storage Location
```
.claude/evals/
├── capability/
│   ├── document-summarizer.eval.md
│   └── code-generator.eval.md
├── regression/
│   ├── classification.eval.md
│   └── extraction.eval.md
├── safety/
│   └── content-filter.eval.md
└── baselines/
    └── v1.0.0.json
```

### Eval Definition Format
```markdown
---
name: document-summarizer
type: capability
version: 1.0.0
created: 2025-01-26
---

# Document Summarizer Eval

## Success Criteria
- [ ] Summary length < 30% of original
- [ ] Contains 80%+ key concepts
- [ ] Grammatically correct
- [ ] No hallucinations

## Test Cases

### Case 1: Short News Article
**Input**: [news_article.txt]
**Expected**: Summary with main event, actors, outcome
**Grader**: model-based

### Case 2: Technical Documentation
**Input**: [tech_doc.md]
**Expected**: Summary with key concepts, no code
**Grader**: hybrid (code + model)

## Grading Configuration
```yaml
method: hybrid
code_checks:
  - length_ratio: 0.3
  - keyword_coverage: 0.8
model_judge:
  criteria: [accuracy, completeness, clarity]
  threshold: 0.8
```

## Targets
- pass@3: > 90%
- average_score: > 0.8
- latency_p99: < 5s
```

---

## Integration

### With `/verify`
```
/verify → General code quality
/eval → AI feature quality

Both must pass for PR Ready
```

### With `/feature-planner`
```
AI feature phase structure:
1. Define eval (RED)
2. Implement (GREEN)
3. Run evaluation
4. Refactor (BLUE)
5. Add regression test
```

### With CI/CD
```yaml
# .github/workflows/eval.yml
eval:
  runs-on: ubuntu-latest
  steps:
    - name: Run Evals
      run: claude eval run --all

    - name: Check Regression
      run: claude eval regression --baseline v1.0.0

    - name: Upload Report
      uses: actions/upload-artifact@v3
      with:
        name: eval-report
        path: .claude/evals/reports/
```

---

## Commands

| Command | Description |
|---------|-------------|
| `/eval define <name>` | Define new evaluation |
| `/eval run <name>` | Run evaluation |
| `/eval run --all` | Run all evaluations |
| `/eval report` | Generate eval report |
| `/eval regression` | Run regression tests |
| `/eval baseline <version>` | Save baseline |

---

## Best Practices

### Eval Writing Principles
1. **Specific Success Criteria**: Avoid vague criteria
2. **Diverse Test Cases**: Include edge cases
3. **Appropriate Grader Selection**: Deterministic vs probabilistic
4. **Realistic Goals**: pass@1 100% is unrealistic

### Anti-Patterns
```
❌ "Result should be good" → Not measurable
❌ Single test case → Overfitting risk
❌ pass@1 > 99% target → Unrealistic
❌ Everything model-based → Cost and consistency issues
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/excatt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
