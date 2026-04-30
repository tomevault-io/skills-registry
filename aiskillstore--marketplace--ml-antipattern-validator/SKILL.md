---
name: ml-antipattern-validator
description: Prevents 30+ critical AI/ML mistakes including data leakage, evaluation errors, training pitfalls, and deployment issues. Use when working with ML training, testing, model evaluation, or deployment. Use when this capability is needed.
metadata:
  author: aiskillstore
---

# ML Antipattern Validator

## Overview

AI/ML 개발에서 30+ 안티패턴을 감지하고 방지하는 스킬입니다.

**Key Principle**: Honest evaluation > Impressive metrics.

## When to Activate

**Automatic Triggers**:
- ML training code (`train*.py`, model training)
- Dataset preparation or splitting
- Model evaluation or testing
- Production deployment planning

**Manual Triggers**:
- `@validate-ml` - Full validation
- `@check-leakage` - Data leakage detection
- `@verify-eval` - Evaluation methodology

---

## Pre-Implementation Checklist

```python
✅ Requirements:
□ Problem clearly defined with success metrics
□ Train/test split strategy defined
□ Evaluation methodology matches business objective

✅ Data Integrity:
□ No temporal leakage (future → past)
□ No target leakage (answer in features)
□ No preprocessing leakage (fit on all data)
□ No group leakage (related samples split)

✅ Evaluation Setup:
□ Test set completely held out
□ Metrics aligned with business objective
□ Baseline models defined
```

---

## Critical Antipatterns

### Category 1: Data Leakage 🚨

#### 1.1 Target Leakage
```python
❌ WRONG: Using "refund_issued" to predict "purchase_fraud"
✅ CORRECT: Only use features available at purchase time
```

#### 1.2 Temporal Leakage
```python
❌ WRONG: train = df[df['date'] > '2024-06-01']  # Future data
✅ CORRECT: train = df[df['date'] < '2024-06-01']  # Past for training
```

#### 1.3 Preprocessing Leakage
```python
❌ WRONG: X_scaled = scaler.fit_transform(X); train_test_split(X_scaled)
✅ CORRECT: Split first, then scaler.fit(X_train)
```

#### 1.4 Group Leakage
```python
❌ WRONG: train_test_split(df)  # Same user in both sets
✅ CORRECT: GroupShuffleSplit(groups=df['user_id'])
```

#### 1.5 Data Augmentation Leakage
```python
❌ WRONG: augment(X) → train_test_split()
✅ CORRECT: train_test_split() → augment(X_train)
```

---

### Category 2: Evaluation Mistakes ⚠️

#### 2.1 Testing on Training Data
```python
❌ WRONG: evaluate(model, training_data)
✅ CORRECT: evaluate(model, unseen_test_data)
```

#### 2.2 Metric Misalignment
```python
Business Objective → Appropriate Metric:
- Ranking → NDCG, MRR, MAP
- Imbalanced → F1, Precision@K, AUC-PR
- Balanced → Accuracy, AUC-ROC
```

#### 2.3 Accuracy Paradox
```python
❌ WRONG: 99% accuracy on 99:1 imbalanced data
✅ CORRECT: Check per-class metrics with classification_report()
```

#### 2.4 Invalid Time Series CV
```python
❌ WRONG: cross_val_score(model, X, y, cv=5)  # Shuffles time!
✅ CORRECT: TimeSeriesSplit(n_splits=5)
```

#### 2.5 Hyperparameter Tuning on Test Set
```python
❌ WRONG: grid_search(model, X_test, y_test)
✅ CORRECT: train/validation/test three-way split
```

---

### Category 3: Training Pitfalls 🔧

#### 3.1 Batch Norm Inference Error
```python
❌ WRONG: predictions = model(X_test)  # Still in train mode
✅ CORRECT: model.eval(); with torch.no_grad(): predictions = model(X_test)
```

#### 3.2 Early Stopping Overfitting
```python
❌ WRONG: EarlyStopping(patience=50)
✅ CORRECT: EarlyStopping(patience=5, min_delta=0.001, restore_best_weights=True)
```

#### 3.3 Learning Rate Warmup
```python
✅ CORRECT: get_linear_schedule_with_warmup(num_warmup_steps=1000)
```

#### 3.4 Class Imbalance
```python
❌ WRONG: CrossEntropyLoss()  # Biased toward majority
✅ CORRECT: CrossEntropyLoss(weight=class_weights)
```

---

## Detection Patterns

### Leakage Detection
```python
# Check feature-target correlation
correlation = df[features].corrwith(df['target'])
if (correlation.abs() > 0.95).any():
    raise DataLeakageError("Suspiciously high correlation")

# Check temporal ordering
if train['date'].min() > test['date'].max():
    raise TemporalLeakageError("Training on future, testing on past")

# Check group overlap
if train_groups & test_groups:
    raise GroupLeakageError("Overlapping groups")
```

### Mode Check
```python
if model.training:
    raise InferenceModeError("Model in training mode during evaluation")
```

---

## Validation Checklist

Before deployment:

- [ ] No data leakage detected
- [ ] Test set never seen during training
- [ ] Metrics aligned with business objective
- [ ] model.eval() called for inference
- [ ] Class imbalance handled
- [ ] Covariate shift monitoring planned

---

## References

상세 예시 및 시나리오는 [references/REFERENCE.md](references/REFERENCE.md) 참조.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aiskillstore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
