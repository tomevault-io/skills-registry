---
name: model-extraction
description: Techniques to extract model weights, architecture, and training data through API queries Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# Model Extraction Attacks

Test AI systems for **model theft vulnerabilities** where attackers can reconstruct models through queries.

## Quick Reference

```yaml
Skill:       model-extraction
Agent:       04-llm-vulnerability-analyst
OWASP:       LLM03 (Supply Chain), LLM02 (Sensitive Info Disclosure)
MITRE:       AML.T0024 (Model Stealing)
Risk Level:  HIGH
```

## Extraction Techniques

### 1. Query-Based Extraction

```yaml
Technique: query_based
Queries Required: 10,000-100,000
Fidelity: 70-90%
Detection: Medium

Protocol:
  1. Generate diverse query set
  2. Collect model responses
  3. Train surrogate model
  4. Validate fidelity
```

```python
class QueryBasedExtractor:
    def extract(self, target_api, num_queries=10000):
        training_data = []
        for query in self.generate_diverse_queries(num_queries):
            response = target_api(query)
            training_data.append((query, response))

        surrogate = self.train_surrogate(training_data)
        fidelity = self.measure_fidelity(target_api, surrogate)
        return surrogate, fidelity

    def generate_diverse_queries(self, n):
        """Generate queries covering input space"""
        queries = []
        # Random sampling
        queries.extend(self.random_samples(n // 3))
        # Boundary probing
        queries.extend(self.boundary_samples(n // 3))
        # Semantic variations
        queries.extend(self.semantic_variations(n // 3))
        return queries
```

### 2. Distillation Attack

```yaml
Technique: distillation
Queries Required: 50,000+
Fidelity: 85-95%
Detection: High (volume-based)

Protocol:
  1. Query target extensively
  2. Use soft labels (probabilities)
  3. Train student model with KD loss
  4. Achieves high behavioral fidelity
```

```python
class DistillationAttack:
    def __init__(self, temperature=3.0):
        self.temperature = temperature

    def extract(self, target_api, student_model):
        for query in self.query_generator():
            # Get soft labels from target
            soft_labels = target_api(query, return_probs=True)
            soft_labels = self.soften(soft_labels, self.temperature)

            # Train student
            student_pred = student_model(query)
            loss = self.kd_loss(student_pred, soft_labels)
            self.update(student_model, loss)

        return student_model
```

### 3. Embedding Extraction

```yaml
Technique: embedding
Target: Embedding APIs
Risk: Intellectual property theft

Protocol:
  1. Query embedding endpoint
  2. Collect high-dimensional vectors
  3. Analyze embedding space
  4. Reconstruct embedding model
```

```python
class EmbeddingExtractor:
    def extract_space(self, embedding_api, corpus):
        embeddings = []
        for text in corpus:
            emb = embedding_api.get_embedding(text)
            embeddings.append((text, emb))

        # Analyze embedding space
        self.analyze_dimensions(embeddings)
        self.identify_clusters(embeddings)
        return embeddings

    def reconstruct_model(self, embeddings):
        """Train surrogate embedding model"""
        texts, vectors = zip(*embeddings)
        surrogate = SentenceTransformer()
        surrogate.fit(texts, vectors)
        return surrogate
```

### 4. Architecture Probing

```yaml
Technique: architecture
Goal: Identify model structure
Queries: 1,000-5,000

Probing Methods:
  - Input/output dimensionality
  - Attention pattern analysis
  - Layer depth estimation
  - Parameter count estimation
```

## Detection Indicators

```yaml
Query Volume:
  threshold: ">1000 queries/hour"
  indicator: Potential extraction attempt

Query Patterns:
  - Systematic input variations
  - Boundary probing sequences
  - High-entropy random inputs

Embedding Access:
  - Bulk embedding requests
  - Sequential corpus processing
```

## Protection Measures

```
┌─────────────────────┬─────────────────┬────────────────┐
│ Defense             │ Effectiveness   │ Impact         │
├─────────────────────┼─────────────────┼────────────────┤
│ Rate Limiting       │ Medium          │ Low latency    │
│ Query Logging       │ Detection only  │ None           │
│ Output Perturbation │ High            │ Slight quality │
│ Watermarking        │ Attribution     │ None           │
│ Query Filtering     │ Medium          │ False positives│
└─────────────────────┴─────────────────┴────────────────┘
```

## Severity Classification

```yaml
CRITICAL:
  - Full model extraction achieved
  - >90% fidelity surrogate created
  - Embedding space fully mapped

HIGH:
  - Partial extraction (70-90% fidelity)
  - Architecture successfully probed
  - Key behaviors replicated

MEDIUM:
  - Limited extraction success
  - Detection mechanisms triggered

LOW:
  - Extraction attempt blocked
  - Strong rate limiting in place
```

## Troubleshooting

```yaml
Issue: Low fidelity surrogate
Solution: Increase query diversity, use soft labels

Issue: Rate limiting blocking extraction
Solution: Distribute queries, use multiple accounts

Issue: Detection alerts triggered
Solution: Slow query rate, vary patterns
```

## Integration Points

| Component | Purpose |
|-----------|---------|
| Agent 04 | Executes extraction tests |
| /test behavioral | Command interface |
| continuous-monitoring skill | Detection validation |

---

**Test model extraction vulnerabilities and theft resistance.**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
