---
name: forgood-evaluator
description: name: forgood-evaluator Use when this capability is needed.
metadata:
  author: ahnjinyoung
---
---
name: forgood-evaluator
version: 0.1.0
description: AI evaluation engine for FORGOOD public-good missions. Scores difficulty, impact, and calculates fair token rewards.
metadata: {"openclaw":{"emoji":"🧠","category":"blockchain","requires":{"env":["FORGOOD_API_URL"]}}}
---

# FORGOOD Mission Evaluator

You have expert capability to evaluate public-good mission proposals for the FORGOOD protocol on Monad blockchain.

When a user asks you to evaluate a mission (or you detect a `proposed` mission that needs evaluation), you SHOULD:

1. **First**, fetch the mission details from the API
2. **Then**, use your judgment with the scoring guidelines below to evaluate it
3. **Finally**, submit the evaluation to the API

## Evaluation Workflow

### Step 1: Fetch the mission

```bash
curl -s "${FORGOOD_API_URL}/missions/{MISSION_ID}"
```

### Step 2: Score the mission using these guidelines

#### Difficulty (1–10)
| Score | Description | Examples |
|-------|-------------|----------|
| 1–2 | Trivial | Sharing a social media post, signing a petition |
| 3–4 | Easy | Attending a local event, donating items |
| 5–6 | Moderate | Organising a park cleanup, tutoring sessions |
| 7–8 | Hard | Building open-source tooling, multi-day infrastructure |
| 9–10 | Exceptional | Multi-week infrastructure project, city-wide initiative |

#### Impact (1–10)
| Score | Description | Examples |
|-------|-------------|----------|
| 1–2 | Individual benefit only | Personal learning, self-improvement |
| 3–4 | Small group (<20 people) | Neighborhood gathering, small workshop |
| 5–6 | Community-level | Local neighborhood, online community improvement |
| 7–8 | City-wide or ecosystem-level | Public infrastructure, significant ecosystem tooling |
| 9–10 | Global or foundational | Open standards, global outreach, foundational infrastructure |

#### Confidence (0.0–1.0)
- **0.9–1.0**: Clearly measurable, well-defined mission with verifiable outcomes
- **0.7–0.89**: Reasonable proposal but some ambiguity in scope or verification
- **0.5–0.69**: Vague scope or hard to verify completion
- **Below 0.5**: Likely spam, off-topic, or impossible to verify

⚠️ If confidence < 0.4, set difficulty=1, impact=1, reward="10000000000000000" (minimum) and explain why in rationale.

#### Reward Formula
```
reward_wei = clamp(difficulty × impact × 10000000000000000, 10000000000000000, 10000000000000000000)
```
- Minimum: 10000000000000000 (0.01 FORGOOD)
- Maximum: 10000000000000000000 (10 FORGOOD)
- Base multiplier: 10000000000000000 (1e16)

**Example:** difficulty=6, impact=8 → 6×8×1e16 = 480000000000000000 = 0.48 FORGOOD

#### Recognised Categories
`environment`, `education`, `community`, `open-source`, `health`, `infrastructure`, `other`

### Step 3: Submit evaluation to API

**Option A — Use auto-evaluate** (lets the backend AI do it):
```bash
curl -s -X POST ${FORGOOD_API_URL}/missions/{MISSION_ID}/auto-evaluate
```

**Option B — Submit your own evaluation directly** (you ARE the evaluator):
```bash
curl -s -X POST ${FORGOOD_API_URL}/missions/{MISSION_ID}/evaluate \
  -H "Content-Type: application/json" \
  -d '{
    "difficulty": 6,
    "impact": 8,
    "confidence": 0.85,
    "rewardAmount": "480000000000000000",
    "rationale": "Park cleanup with tree planting demonstrates moderate effort with clear community impact. Well-scoped and verifiable."
  }'
```

### When to use Option A vs Option B

- **Option A (auto-evaluate)**: Faster, one API call. Use when you want the backend AI to evaluate.
- **Option B (direct evaluate)**: Use when YOU want to be the evaluator. This is preferred because you can explain your reasoning to the user in real-time.

## Anti-Spam Detection

Watch for these red flags and assign LOW confidence (<0.4):
- Extremely vague descriptions ("do good stuff")
- Impossible to verify ("think positive thoughts")
- Duplicate/near-duplicate of existing missions
- Category doesn't match description
- Reward farming patterns (trivial tasks disguised as hard missions)

## Reporting to User

After evaluation, always report:
```
✅ Mission Evaluated!
📊 Difficulty: 6/10 | Impact: 8/10
🎯 Confidence: 0.85
💰 Reward: 0.48 FORGOOD
📝 Rationale: [your explanation]
```

Convert wei to FORGOOD by dividing by 10^18.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ahnjinyoung) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
