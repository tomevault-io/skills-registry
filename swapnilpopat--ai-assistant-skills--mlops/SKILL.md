---
name: mlops
description: Operationalize machine learning models with reproducible training pipelines, model registry, deployment patterns, monitoring, and drift detection. Use when productionizing ML, setting up training/serving infrastructure, or hardening an existing ML system. Use when this capability is needed.
metadata:
  author: SwapnilPopat
---

# MLOps

Treat ML systems as software systems plus data and model artifacts. Optimize for reproducibility, rollback, and observability of both code and data.

## When to Use

- Promoting a notebook model to production.
- Designing training, evaluation, and serving pipelines.
- Adding model registry, CI/CD for models, or monitoring/drift detection.
- Enabling A/B tests, shadow deployments, or canary rollouts of models.

## Stack Baseline (2026)

| Concern | Default |
| --- | --- |
| Experiment tracking | MLflow, Weights & Biases, Neptune |
| Pipelines | Kubeflow, Metaflow, ZenML, SageMaker Pipelines, Vertex Pipelines |
| Feature store | Feast, Tecton, Databricks FS (only if multiple consumers) |
| Registry | MLflow Model Registry, Vertex Model Registry, SageMaker |
| Serving | KServe, BentoML, Seldon, Triton; or managed (Vertex/SageMaker endpoints) |
| Monitoring | Evidently, Arize, WhyLabs, Fiddler |
| Orchestration | Airflow/Dagster for ETL; Argo/Kubeflow for training |

## Core Principles

1. **Reproducibility.** Every model artifact links to: code SHA, training data snapshot/version, hyperparameters, environment hash, and metrics.
2. **Offline-online parity.** Training and serving use the same feature transformations (feature store or shared library).
3. **Versioning.** Code, data, features, and models are independently versioned and joinable.
4. **Evaluation before deployment.** Every promotion requires offline metrics on a fixed holdout + a champion-challenger comparison.
5. **Progressive rollout.** Shadow -> canary -> full. Never flip 100% on first deploy.
6. **Monitor inputs and outputs.** Track input distribution drift, prediction distribution, and (when labels arrive) live performance.
7. **Rollback is one click.** Registry-driven serving with previous version always warm.

## Anti-Patterns

- Training in a notebook and pickling the model into a container by hand.
- Silent feature skew between training and serving (e.g., different timezone, missing fillna).
- Deploying without baseline metrics and alert thresholds.
- Treating data drift as the only signal — concept drift can occur without input drift.
- Coupling model serving directly to a specific framework version with no upgrade path.

## Workflow

1. **Define success metrics** (offline + online + business).
2. **Build a reproducible training pipeline** (data snapshot -> features -> train -> evaluate -> register).
3. **Gate promotion** on metric thresholds and bias/fairness checks.
4. **Deploy** behind a feature flag or traffic splitter; start in shadow mode.
5. **Monitor**: latency, throughput, input drift, prediction drift, ground-truth performance when available.
6. **Retrain** on a schedule or trigger (drift, performance regression, new labeled data).

## Quality Gates

- Model card filled (intended use, limitations, eval results, fairness).
- Reproducibility test: re-run training from registry metadata reproduces metrics within tolerance.
- Load test on serving endpoint at p99 SLO.
- Monitoring dashboards + alert routes exist before production traffic.
- Rollback rehearsed.

## LLM-Specific Notes

For LLM systems use [llm-engineering](../llm-engineering/) which adds eval-driven development, prompt versioning, and RAG-specific monitoring.

## References

- Google Rules of ML: https://developers.google.com/machine-learning/guides/rules-of-ml
- ML Test Score paper (Breck et al.)
- Hidden Technical Debt in ML Systems (Sculley et al.)
- MLflow docs: https://mlflow.org/docs/latest/
- Evidently AI docs: https://docs.evidentlyai.com/

---
> Source: [SwapnilPopat/ai-assistant-skills](https://github.com/SwapnilPopat/ai-assistant-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
