---
name: ai-ml-ops
description: Universal ML operations expert for model lifecycle, deployment, monitoring, optimization Use when this capability is needed.
metadata:
  author: seqis
---

# AI/ML Operations Skill

**Purpose**: Production-grade MLOps patterns for model lifecycle management, deployment, monitoring, and CI/CD across all ML domains.

**Invoke When**: ML model development, deployment, monitoring, drift detection, retraining, A/B testing, feature stores, compliance, cost optimization, explainability.

---

## ML Domain Quick Reference

| Domain | Key Models | Key Considerations |
|--------|------------|-------------------|
| Computer Vision | ResNet, ViT, YOLO, Mask R-CNN, SAM | Large datasets (100GB+), GPU training, edge deployment, FDA compliance for medical |
| NLP | BERT, GPT, T5, DeBERTa | Token costs, prompt versioning, semantic drift, PII handling |
| Recommenders | Two-tower, NCF, GRU4Rec | Cold start, real-time personalization, diversity metrics |
| Time Series | Prophet, DeepAR, TFT | Walk-forward validation, concept drift, uncertainty quantification |
| Fraud Detection | XGBoost, GNN, Autoencoders | Class imbalance (1:1000+), <50ms latency, PCI DSS |
| Search/Ranking | BM25, DPR, Cross-encoders | Two-stage retrieval, NDCG/MRR, index versioning |
| Speech | Wav2Vec, Whisper, Tacotron | Streaming ASR, WER monitoring, accent bias |
| Reinforcement Learning | PPO, SAC, DQN | Replay buffers, safety constraints, sim-to-real |

---

## Core MLOps Infrastructure

### 1. Experiment Tracking

**Tools**: MLflow, W&B, Neptune, Comet, TensorBoard

```python
# MLflow comprehensive tracking
import mlflow

mlflow.set_tracking_uri("https://mlflow.company.com")
mlflow.set_experiment("fraud-detection")

with mlflow.start_run(run_name="xgb-v5"):
    mlflow.log_params({"max_depth": 8, "learning_rate": 0.1})
    mlflow.log_metrics({"precision": 0.92, "recall": 0.87, "f1": 0.89})
    mlflow.log_artifact("confusion_matrix.png")
    mlflow.xgboost.log_model(model, "model", registered_model_name="fraud-detector")
    mlflow.set_tags({"team": "risk-ml", "production_ready": "true"})
```

**Track**: Performance metrics, training time, GPU usage, data stats, hyperparameters, model artifacts.

---

### 2. Model Registry

**Tools**: MLflow Registry, SageMaker, Azure ML, Vertex AI

**Stage Lifecycle**:
- `None/Development`: Experimental
- `Staging`: Passing validation, ready for online testing
- `Production`: Serving traffic
- `Archived`: Deprecated, kept for audit/rollback

```python
from mlflow.tracking import MlflowClient
client = MlflowClient()

# Register and promote
version = client.create_model_version(name="fraud-detector", source=model_uri)
client.transition_model_version_stage(name="fraud-detector", version=version.version, stage="Staging")

# Validate then promote to production
if validation_passes:
    client.transition_model_version_stage(name="fraud-detector", version=version.version,
                                          stage="Production", archive_existing_versions=True)
```

---

### 3. Feature Stores

**Tools**: Feast, Tecton, Hopsworks, SageMaker Feature Store, Vertex AI

**Benefits**: Training/serving consistency, feature reuse, point-in-time correctness, low-latency serving.

```python
from feast import FeatureStore, Entity, FeatureView, Field
from feast.types import Float32, Int64

# Define
user = Entity(name="user_id", join_keys=["user_id"])
user_features = FeatureView(
    name="user_features", entities=[user],
    schema=[Field(name="avg_transaction_7d", dtype=Float32)],
    source=FileSource(path="s3://features/users.parquet")
)

# Training: historical features
fs = FeatureStore(repo_path=".")
training_df = fs.get_historical_features(entity_df=entities, features=["user_features:avg_transaction_7d"]).to_df()

# Inference: online features (<10ms)
online = fs.get_online_features(entity_rows=[{"user_id": "123"}], features=[...]).to_dict()
```

---

### 4. Model Serving

**Tools**: TensorFlow Serving, TorchServe, Seldon, KServe, BentoML, Triton

**Patterns**:
- **Real-time**: REST/gRPC API, <100ms latency
- **Batch**: Spark UDF, scheduled jobs
- **Streaming**: Kafka consumers, Flink

```python
# BentoML serving
import bentoml

@bentoml.service(resources={"cpu": "2", "memory": "4Gi"})
class FraudService:
    model = bentoml.sklearn.get("fraud_detector:latest").to_runner()

    @bentoml.api(input=JSON(), output=JSON())
    async def predict(self, data: dict) -> dict:
        return {"fraud_prob": await self.model.async_run(extract_features(data))}
```

**Optimization**: Quantization (INT8/FP16), pruning, distillation, ONNX, TensorRT, batching.

---

### 5. Monitoring & Observability

**Tools**: Evidently, WhyLabs, Arize, Fiddler, Prometheus/Grafana

**Monitor**:
1. **Data Drift**: Input distribution changes
2. **Prediction Drift**: Output distribution changes
3. **Model Performance**: Accuracy degradation
4. **System Metrics**: Latency, throughput, errors

```python
# Evidently drift detection
from evidently.report import Report
from evidently.metric_preset import DataDriftPreset

report = Report(metrics=[DataDriftPreset()])
report.run(reference_data=training_data, current_data=production_data)

if report.as_dict()['metrics'][0]['result']['drift_detected']:
    trigger_retraining()
```

```python
# Prometheus metrics
from prometheus_client import Counter, Histogram

prediction_counter = Counter('predictions_total', 'Predictions', ['model', 'version'])
latency = Histogram('prediction_latency_seconds', 'Latency', ['model'])

@latency.labels(model='fraud').time()
def predict(data):
    result = model.predict(data)
    prediction_counter.labels(model='fraud', version='v5').inc()
    return result
```

---

### 6. ML Pipelines & Orchestration

**Tools**: Kubeflow, Airflow, Prefect, Metaflow, Kedro, Step Functions

```python
# Kubeflow pipeline
from kfp import dsl
from kfp.components import create_component_from_func

@dsl.pipeline(name='Fraud Training')
def pipeline(data_path: str):
    load = load_data(data_path)
    train = train_model(load.output).after(load)
    evaluate = eval_model(train.output).after(train)
    deploy = deploy_if_good(evaluate.output).after(evaluate)
```

```python
# Airflow DAG
from airflow import DAG
from airflow.operators.python import PythonOperator

with DAG('retraining', schedule='0 2 * * 0') as dag:
    check_drift >> train >> evaluate >> deploy
```

---

### 7. A/B Testing & Deployment Strategies

**Strategies**:
- **A/B Test**: Split traffic, measure business impact
- **Canary**: Gradual rollout (5% -> 25% -> 100%)
- **Shadow**: Parallel scoring, compare outputs
- **Multi-Armed Bandit**: Dynamic allocation

```python
# A/B routing
class ABRouter:
    def route(self, user_id: str) -> str:
        hash_val = int(hashlib.md5(user_id.encode()).hexdigest(), 16)
        return "model_b" if (hash_val % 100) < (traffic_split * 100) else "model_a"
```

```python
# Canary with auto-rollback
class CanaryDeployment:
    def route(self, features):
        if random.random() < canary_pct and error_rate < 0.05:
            return canary_model.predict(features)
        return stable_model.predict(features)
```

---

### 8. Explainability & Fairness

**Tools**: SHAP, LIME, IntegratedGradients, Alibi, Captum, AIF360

```python
# SHAP explanations
import shap
explainer = shap.TreeExplainer(model)
shap_values = explainer.shap_values(X)

# Top contributing features
contributions = sorted(zip(features, shap_values[0]), key=lambda x: abs(x[1]), reverse=True)[:5]
```

```python
# AIF360 fairness audit
from aif360.metrics import ClassificationMetric

metric = ClassificationMetric(dataset, predictions,
    unprivileged_groups=[{'gender': 0}], privileged_groups=[{'gender': 1}])

fairness = {
    "disparate_impact": metric.disparate_impact(),  # Should be 0.8-1.25
    "equal_opportunity_diff": metric.equal_opportunity_difference()
}
```

---

## Cloud Platform Patterns

### AWS SageMaker
```python
from sagemaker.xgboost import XGBoost
xgb = XGBoost(entry_point="train.py", role=role, instance_type="ml.m5.xlarge",
              use_spot_instances=True, max_wait=7200)
xgb.fit({"train": "s3://bucket/train"})
predictor = xgb.deploy(instance_count=2, instance_type="ml.m5.large")
```

### Azure ML
```python
from azureml.core import Workspace, Experiment
ws = Workspace.from_config()
run = Experiment(ws, "fraud").submit(ScriptRunConfig(...))
model = run.register_model("fraud-detector", model_path="outputs/model.pkl")
```

### Vertex AI
```python
from google.cloud import aiplatform
job = aiplatform.CustomTrainingJob(display_name="training", script_path="train.py", ...)
model = job.run(dataset=dataset, machine_type="n1-standard-4")
endpoint = model.deploy(machine_type="n1-standard-2", min_replica_count=2)
```

---

## Cost Optimization

### Training
- **Spot instances**: 60-80% savings
- **Mixed precision**: 2-3x speedup
- **Gradient accumulation**: Larger batches on smaller GPUs
- **Checkpointing**: Resume interrupted jobs

### Inference
- **Quantization**: INT8 for 2-4x speedup
- **Auto-scaling**: Scale to zero during low traffic
- **Batch inference**: Amortize overhead
- **Caching**: Cache repeated predictions

---

## Compliance & Governance

| Domain | Requirements |
|--------|-------------|
| Healthcare | FDA 510(k), HIPAA, model validation docs |
| Finance | SOX, PCI DSS, explainability for decisions |
| General | GDPR right to explanation, fairness audits |

```python
# Model governance
class ModelGovernance:
    def register_with_audit(self, model_uri, metrics, approvers):
        version = client.create_model_version(name=self.model_name, source=model_uri)
        for k, v in metrics.items():
            client.set_model_version_tag(self.model_name, version.version, f"metric_{k}", str(v))
        client.set_model_version_tag(self.model_name, version.version, "approvers", ",".join(approvers))
```

---

## Incident Response

| Incident | Response |
|----------|----------|
| Performance degradation | Alert, rollback to previous version, trigger retraining |
| Data drift | Alert, enable shadow mode, schedule retraining |
| System failure | Page on-call, activate fallback, create incident ticket |
| Compliance violation | Halt deployment, notify compliance team, audit |

```python
def handle_degradation(current_metrics, thresholds):
    if current_metrics['f1'] < thresholds['f1']:
        send_pagerduty_alert(severity="critical")
        rollback_to_previous_version()
        trigger_emergency_retraining()
```

---

## Best Practices Checklist

### Development
- [ ] Version data, code, models, configs
- [ ] Experiment tracking from day one
- [ ] Reproducible training pipelines
- [ ] Document assumptions and limitations

### Training
- [ ] Proper data splits (temporal, stratified)
- [ ] Real-time metric monitoring
- [ ] Early stopping, checkpointing
- [ ] Track compute costs

### Deployment
- [ ] Shadow/canary before full rollout
- [ ] Automated rollback capability
- [ ] Both model and system monitoring
- [ ] Documented procedures

### Monitoring
- [ ] Data, model, prediction drift detection
- [ ] Fairness metrics
- [ ] Automated retraining triggers
- [ ] Stakeholder dashboards

### Compliance
- [ ] Audit logs for all changes
- [ ] Validation and approval workflows
- [ ] Explainability for regulated domains
- [ ] Traceable lineage

---

*Philosophy: Production ML requires engineering discipline. It's not just accuracy - it's reliability, scalability, explainability, fairness, and cost-effectiveness across the entire lifecycle.*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seqis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
