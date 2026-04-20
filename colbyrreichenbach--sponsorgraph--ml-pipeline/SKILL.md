---
name: ml-pipeline-expert
description: Vertex AI model training, feature engineering, model deployment, monitoring. Use when building ML pipelines, training models, or implementing MLOps workflows. Use when this capability is needed.
metadata:
  author: colbyrreichenbach
---

# ML Pipeline Expert Skill

## When to Use This Skill

Activate when:
- Training pricing prediction models
- Building feature engineering pipelines
- Deploying models to Vertex AI
- Setting up model monitoring
- Implementing MLOps workflows
- Debugging model performance issues

## Core Patterns

### 1. Feature Engineering in BigQuery

```sql
-- models/ml/features_pricing_model.sql
-- Feature table for creator pricing prediction

WITH creator_stats AS (
    SELECT
        creator_id,
        platform,
        follower_count,
        AVG(engagement_rate) AS avg_engagement_rate,
        COUNT(DISTINCT video_id) AS video_count_30d,
        AVG(views) AS avg_views_per_video
    FROM {{ ref('int_creator_video_metrics') }}
    WHERE published_at >= DATE_SUB(CURRENT_DATE(), INTERVAL 30 DAY)
    GROUP BY 1, 2, 3
),

audience_quality AS (
    SELECT
        creator_id,
        AVG(watch_time_seconds / duration_seconds) AS avg_completion_rate,
        COUNT(DISTINCT country) AS country_diversity,
        -- Audience demographics (derived from platform insights)
        AVG(CASE WHEN age_group = '18-24' THEN 1 ELSE 0 END) AS pct_age_18_24,
        AVG(CASE WHEN age_group = '25-34' THEN 1 ELSE 0 END) AS pct_age_25_34
    FROM {{ ref('int_audience_demographics') }}
    GROUP BY 1
),

historical_deals AS (
    SELECT
        creator_id,
        AVG(deal_value_usd) AS avg_historical_rate,
        COUNT(*) AS deal_count,
        MAX(deal_date) AS last_deal_date
    FROM {{ ref('fact_creator_deals') }}
    WHERE deal_date >= DATE_SUB(CURRENT_DATE(), INTERVAL 365 DAY)
    GROUP BY 1
),

niche_benchmarks AS (
    SELECT
        c.creator_id,
        c.niche_category,
        AVG(d.deal_value_usd) AS niche_avg_rate
    FROM {{ ref('dim_creator') }} c
    LEFT JOIN {{ ref('fact_creator_deals') }} d
        ON c.niche_category = d.niche_category
    WHERE d.deal_date >= DATE_SUB(CURRENT_DATE(), INTERVAL 365 DAY)
    GROUP BY 1, 2
),

features AS (
    SELECT
        cs.creator_id,
        cs.platform,
        
        -- Raw features
        cs.follower_count,
        cs.avg_engagement_rate,
        cs.video_count_30d,
        cs.avg_views_per_video,
        
        -- Derived features
        LOG(cs.follower_count + 1) AS log_follower_count,
        cs.avg_views_per_video / NULLIF(cs.follower_count, 0) AS view_rate,
        
        -- Audience quality
        aq.avg_completion_rate,
        aq.country_diversity,
        aq.pct_age_18_24,
        aq.pct_age_25_34,
        
        -- Historical context
        hd.avg_historical_rate,
        hd.deal_count,
        DATE_DIFF(CURRENT_DATE(), hd.last_deal_date, DAY) AS days_since_last_deal,
        
        -- Niche context
        nb.niche_avg_rate,
        
        -- Target variable (for training)
        hd.avg_historical_rate AS target_rate
        
    FROM creator_stats cs
    LEFT JOIN audience_quality aq ON cs.creator_id = aq.creator_id
    LEFT JOIN historical_deals hd ON cs.creator_id = hd.creator_id
    LEFT JOIN niche_benchmarks nb ON cs.creator_id = nb.creator_id
    WHERE hd.avg_historical_rate IS NOT NULL  -- Only creators with deal history
)

SELECT * FROM features
```

**Feature Engineering Best Practices**:
- **Log transforms**: For skewed distributions (follower_count)
- **Ratios**: More informative than raw counts (view_rate)
- **Temporal features**: Days since last event
- **Categorical encoding**: One-hot or target encoding
- **Null handling**: Use NULLIF, COALESCE

### 2. Train/Test Split in BigQuery

```sql
-- models/ml/features_pricing_model_split.sql

WITH features AS (
    SELECT * FROM {{ ref('features_pricing_model') }}
),

split AS (
    SELECT
        *,
        -- Deterministic split based on creator_id hash
        MOD(ABS(FARM_FINGERPRINT(creator_id)), 100) AS random_bucket
    FROM features
)

SELECT
    * EXCEPT(random_bucket),
    CASE 
        WHEN random_bucket < 80 THEN 'train'
        WHEN random_bucket < 90 THEN 'val'
        ELSE 'test'
    END AS split
FROM split
```

**Split Strategy**:
- **80/10/10**: Train/Val/Test
- **Deterministic**: Same creator always in same split
- **Stratified**: Ensure balanced target distribution

### 3. Model Training Pipeline (Python)

```python
# src/ml/train_pricing_model.py

from google.cloud import bigquery, aiplatform
from sklearn.ensemble import GradientBoostingRegressor
from sklearn.metrics import mean_absolute_error, r2_score
import pandas as pd
import joblib
from datetime import datetime

class PricingModelTrainer:
    """
    Train XGBoost pricing model using BigQuery features
    """
    
    def __init__(self, project_id: str, dataset: str):
        self.project_id = project_id
        self.dataset = dataset
        self.client = bigquery.Client(project=project_id)
        
    def load_features(self, split: str) -> pd.DataFrame:
        """Load features from BigQuery"""
        query = f"""
        SELECT * EXCEPT(creator_id, split)
        FROM `{self.project_id}.{self.dataset}.features_pricing_model_split`
        WHERE split = '{split}'
        """
        return self.client.query(query).to_dataframe()
    
    def train(self, config: dict) -> dict:
        """
        Train pricing model
        
        Args:
            config: Model hyperparameters
            
        Returns:
            metrics: Training and validation metrics
        """
        # Load data
        train_df = self.load_features('train')
        val_df = self.load_features('val')
        
        # Separate features and target
        feature_cols = [
            'log_follower_count',
            'avg_engagement_rate',
            'video_count_30d',
            'view_rate',
            'avg_completion_rate',
            'country_diversity',
            'pct_age_18_24',
            'pct_age_25_34',
            'days_since_last_deal',
            'niche_avg_rate'
        ]
        
        X_train = train_df[feature_cols]
        y_train = train_df['target_rate']
        
        X_val = val_df[feature_cols]
        y_val = val_df['target_rate']
        
        # Handle missing values
        X_train = X_train.fillna(X_train.median())
        X_val = X_val.fillna(X_train.median())
        
        # Train model
        model = GradientBoostingRegressor(
            n_estimators=config.get('n_estimators', 100),
            learning_rate=config.get('learning_rate', 0.1),
            max_depth=config.get('max_depth', 5),
            random_state=42
        )
        
        model.fit(X_train, y_train)
        
        # Evaluate
        train_preds = model.predict(X_train)
        val_preds = model.predict(X_val)
        
        metrics = {
            'train_mae': mean_absolute_error(y_train, train_preds),
            'train_r2': r2_score(y_train, train_preds),
            'val_mae': mean_absolute_error(y_val, val_preds),
            'val_r2': r2_score(y_val, val_preds),
            'feature_importance': dict(zip(
                feature_cols,
                model.feature_importances_
            ))
        }
        
        # Save model
        timestamp = datetime.now().strftime('%Y%m%d_%H%M%S')
        model_path = f'models/pricing_model_{timestamp}.joblib'
        joblib.dump(model, model_path)
        
        return metrics, model_path
    
    def evaluate_test_set(self, model_path: str) -> dict:
        """Evaluate on held-out test set"""
        model = joblib.load(model_path)
        test_df = self.load_features('test')
        
        feature_cols = [c for c in test_df.columns if c != 'target_rate']
        X_test = test_df[feature_cols].fillna(test_df[feature_cols].median())
        y_test = test_df['target_rate']
        
        test_preds = model.predict(X_test)
        
        return {
            'test_mae': mean_absolute_error(y_test, test_preds),
            'test_r2': r2_score(y_test, test_preds),
            'test_samples': len(test_df)
        }


# Usage
if __name__ == "__main__":
    trainer = PricingModelTrainer(
        project_id='sponsorgraph-prod',
        dataset='ml'
    )
    
    config = {
        'n_estimators': 200,
        'learning_rate': 0.05,
        'max_depth': 6
    }
    
    metrics, model_path = trainer.train(config)
    print(f"Training complete: {metrics}")
    
    test_metrics = trainer.evaluate_test_set(model_path)
    print(f"Test set performance: {test_metrics}")
```

### 4. Deploy to Vertex AI

```python
# src/ml/deploy_model.py

from google.cloud import aiplatform
from google.cloud import storage
import joblib

def upload_model_to_gcs(local_path: str, bucket_name: str) -> str:
    """Upload model to GCS"""
    storage_client = storage.Client()
    bucket = storage_client.bucket(bucket_name)
    
    blob_name = f"models/{local_path.split('/')[-1]}"
    blob = bucket.blob(blob_name)
    blob.upload_from_filename(local_path)
    
    return f"gs://{bucket_name}/{blob_name}"

def deploy_to_vertex_ai(
    model_path: str,
    model_name: str,
    project_id: str,
    location: str = 'us-central1'
) -> str:
    """
    Deploy model to Vertex AI endpoint
    
    Returns:
        endpoint_id: Deployed endpoint ID
    """
    aiplatform.init(project=project_id, location=location)
    
    # Upload model
    model = aiplatform.Model.upload(
        display_name=model_name,
        artifact_uri=model_path,
        serving_container_image_uri='us-docker.pkg.dev/vertex-ai/prediction/sklearn-cpu.1-0:latest'
    )
    
    # Create endpoint
    endpoint = aiplatform.Endpoint.create(
        display_name=f"{model_name}_endpoint"
    )
    
    # Deploy model to endpoint
    model.deploy(
        endpoint=endpoint,
        deployed_model_display_name=model_name,
        machine_type='n1-standard-4',
        min_replica_count=1,
        max_replica_count=5,
        traffic_percentage=100
    )
    
    return endpoint.resource_name


# Usage
if __name__ == "__main__":
    # Upload model to GCS
    gcs_path = upload_model_to_gcs(
        local_path='models/pricing_model_20260207.joblib',
        bucket_name='sponsorgraph-models'
    )
    
    # Deploy to Vertex AI
    endpoint_id = deploy_to_vertex_ai(
        model_path=gcs_path,
        model_name='pricing_model_v1',
        project_id='sponsorgraph-prod'
    )
    
    print(f"Model deployed to endpoint: {endpoint_id}")
```

### 5. Model Monitoring

```python
# src/ml/monitor_model.py

from google.cloud import bigquery, monitoring_v3
from datetime import datetime, timedelta
import pandas as pd

class ModelMonitor:
    """Monitor model predictions and detect drift"""
    
    def __init__(self, project_id: str):
        self.project_id = project_id
        self.bq_client = bigquery.Client(project=project_id)
        
    def log_prediction(self, prediction_data: dict):
        """Log prediction to BigQuery for monitoring"""
        table_id = f"{self.project_id}.ml.model_predictions"
        
        rows_to_insert = [{
            'prediction_id': prediction_data['prediction_id'],
            'creator_id': prediction_data['creator_id'],
            'predicted_rate': prediction_data['predicted_rate'],
            'features': prediction_data['features'],
            'model_version': prediction_data['model_version'],
            'timestamp': datetime.utcnow().isoformat()
        }]
        
        errors = self.bq_client.insert_rows_json(table_id, rows_to_insert)
        if errors:
            print(f"Errors logging prediction: {errors}")
    
    def check_prediction_drift(self, days: int = 7) -> dict:
        """
        Check if prediction distribution has drifted
        
        Returns:
            drift_metrics: Mean, std, quantiles over time
        """
        query = f"""
        WITH recent_predictions AS (
            SELECT
                DATE(timestamp) AS pred_date,
                predicted_rate
            FROM `{self.project_id}.ml.model_predictions`
            WHERE timestamp >= TIMESTAMP_SUB(CURRENT_TIMESTAMP(), INTERVAL {days} DAY)
        ),
        
        daily_stats AS (
            SELECT
                pred_date,
                AVG(predicted_rate) AS mean_pred,
                STDDEV(predicted_rate) AS std_pred,
                APPROX_QUANTILES(predicted_rate, 100)[OFFSET(50)] AS median_pred,
                COUNT(*) AS prediction_count
            FROM recent_predictions
            GROUP BY 1
            ORDER BY 1
        )
        
        SELECT * FROM daily_stats
        """
        
        df = self.bq_client.query(query).to_dataframe()
        
        # Calculate drift score (coefficient of variation over time)
        drift_score = df['std_pred'].mean() / df['mean_pred'].mean()
        
        return {
            'drift_score': drift_score,
            'daily_stats': df.to_dict('records'),
            'alert': drift_score > 0.3  # Alert if CV > 30%
        }
    
    def compare_with_actuals(self) -> dict:
        """
        Compare predictions with actual deal values
        For predictions where we later got actual data
        """
        query = f"""
        WITH predictions AS (
            SELECT
                creator_id,
                predicted_rate,
                timestamp AS pred_timestamp
            FROM `{self.project_id}.ml.model_predictions`
        ),
        
        actuals AS (
            SELECT
                creator_id,
                deal_value_usd AS actual_rate,
                deal_date
            FROM `{self.project_id}.mart.fact_creator_deals`
        ),
        
        matched AS (
            SELECT
                p.creator_id,
                p.predicted_rate,
                a.actual_rate,
                ABS(p.predicted_rate - a.actual_rate) AS absolute_error,
                ABS(p.predicted_rate - a.actual_rate) / a.actual_rate AS pct_error
            FROM predictions p
            INNER JOIN actuals a
                ON p.creator_id = a.creator_id
                AND DATE(a.deal_date) >= DATE(p.pred_timestamp)
                AND DATE(a.deal_date) <= DATE_ADD(DATE(p.pred_timestamp), INTERVAL 30 DAY)
        )
        
        SELECT
            AVG(absolute_error) AS mae,
            AVG(pct_error) AS mape,
            COUNT(*) AS sample_size
        FROM matched
        """
        
        result = self.bq_client.query(query).to_dataframe()
        return result.iloc[0].to_dict()


# Usage
if __name__ == "__main__":
    monitor = ModelMonitor(project_id='sponsorgraph-prod')
    
    # Check for drift
    drift_metrics = monitor.check_prediction_drift(days=7)
    
    if drift_metrics['alert']:
        print("⚠️ Model drift detected!")
        print(f"Drift score: {drift_metrics['drift_score']:.3f}")
    
    # Compare with actuals
    performance = monitor.compare_with_actuals()
    print(f"Model MAE: ${performance['mae']:.2f}")
    print(f"Model MAPE: {performance['mape']*100:.1f}%")
```

### 6. Hyperparameter Tuning with Vertex AI

```python
# src/ml/tune_hyperparameters.py

from google.cloud import aiplatform
from google.cloud.aiplatform import hyperparameter_tuning as hpt

def tune_hyperparameters(
    project_id: str,
    training_script: str,
    parameter_spec: dict
) -> str:
    """
    Run hyperparameter tuning job on Vertex AI
    
    Args:
        project_id: GCP project
        training_script: Path to training script
        parameter_spec: Hyperparameter search space
        
    Returns:
        best_trial_id: ID of best hyperparameter combination
    """
    aiplatform.init(project=project_id, location='us-central1')
    
    # Define hyperparameter tuning job
    job = aiplatform.HyperparameterTuningJob(
        display_name='pricing_model_tuning',
        custom_job=aiplatform.CustomJob.from_local_script(
            display_name='pricing_model_training',
            script_path=training_script,
            container_uri='us-docker.pkg.dev/vertex-ai/training/scikit-learn-cpu.1-0:latest',
            requirements=['scikit-learn==1.3.0', 'google-cloud-bigquery==3.10.0']
        ),
        metric_spec={
            'val_mae': 'minimize'
        },
        parameter_spec=parameter_spec,
        max_trial_count=20,
        parallel_trial_count=5
    )
    
    # Run tuning
    job.run()
    
    # Get best trial
    best_trial = min(job.trials, key=lambda t: t.final_measurement.metrics[0].value)
    
    print(f"Best trial: {best_trial.id}")
    print(f"Best MAE: {best_trial.final_measurement.metrics[0].value:.2f}")
    print(f"Best parameters: {best_trial.parameters}")
    
    return best_trial.id


# Define search space
parameter_spec = {
    'n_estimators': hpt.IntegerParameterSpec(min=50, max=300, scale='linear'),
    'learning_rate': hpt.DoubleParameterSpec(min=0.01, max=0.3, scale='log'),
    'max_depth': hpt.IntegerParameterSpec(min=3, max=10, scale='linear'),
}

# Run tuning
best_trial = tune_hyperparameters(
    project_id='sponsorgraph-prod',
    training_script='src/ml/train_pricing_model.py',
    parameter_spec=parameter_spec
)
```

## Model Performance Targets

Based on business_logic.md benchmarks:

```python
MODEL_PERFORMANCE_TARGETS = {
    'pricing_model': {
        'mae': 0.15,  # Mean Absolute Error < 15%
        'r2': 0.80,   # R² > 0.80 (explain 80% of variance)
        'mape': 0.20, # Mean Absolute Percentage Error < 20%
    },
    
    'attribution_model': {
        'accuracy': 0.90,  # 90% accuracy vs holdout
    },
    
    'conversion_prediction': {
        'mae': 0.20,  # Within 20% of actual
        'confidence_interval': 0.85,
    }
}
```

## MLOps Workflow

```bash
# 1. Feature engineering
dbt run --select features_pricing_model

# 2. Train model
python src/ml/train_pricing_model.py --config configs/pricing_v1.yaml

# 3. Evaluate on test set
python src/ml/evaluate_model.py --model-path models/pricing_model_20260207.joblib

# 4. Deploy to Vertex AI
python src/ml/deploy_model.py --model-path models/pricing_model_20260207.joblib

# 5. Monitor predictions
python src/ml/monitor_model.py --check-drift --days 7
```

## Best Practices

### 1. Version Everything

```python
MODEL_VERSION = "v1.2.3"
FEATURE_VERSION = "v2.0.0"
TRAINING_DATE = "2026-02-07"

model_metadata = {
    'model_version': MODEL_VERSION,
    'feature_version': FEATURE_VERSION,
    'training_date': TRAINING_DATE,
    'training_samples': len(train_df),
    'val_mae': val_mae,
    'feature_columns': feature_cols
}

# Save with model
joblib.dump({
    'model': model,
    'metadata': model_metadata
}, model_path)
```

### 2. Feature Store Pattern

```sql
-- Centralized feature table
-- models/ml/feature_store.sql

{{ config(
    materialized='table',
    partition_by={'field': 'feature_timestamp', 'data_type': 'timestamp'}
) }}

SELECT
    creator_id,
    feature_timestamp,
    
    -- Features
    follower_count,
    avg_engagement_rate,
    -- ... all features
    
    -- Metadata
    'v2.0.0' AS feature_version
FROM {{ ref('int_creator_features') }}
```

### 3. Separate Training/Serving Features

```python
# Training: Use historical features
train_features = """
SELECT * FROM ml.features_pricing_model
WHERE feature_timestamp <= '2025-12-31'  # Historical cutoff
"""

# Serving: Use current features
serve_features = """
SELECT * FROM ml.feature_store
WHERE feature_timestamp = (SELECT MAX(feature_timestamp) FROM ml.feature_store)
"""
```

## Related Resources

- Main context: `.claude/CLAUDE.md`
- Business logic: `.claude/business_logic.md`
- BigQuery skill: `.claude/skills/bigquery/SKILL.md`
- Vertex AI docs: https://cloud.google.com/vertex-ai/docs

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/colbyrreichenbach) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
