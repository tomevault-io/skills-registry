---
name: airflow-patterns
description: Apache Airflow workflow patterns for DAG design, operators, and production orchestration. Use when this capability is needed.
metadata:
  author: cemalcici
---

# Apache Airflow Patterns

> **Learn to THINK in dependencies, not just tasks.**

## ⚠️ Core Principles

### DAGs Define Dependencies
- Tasks are nodes, edges are dependencies
- Parallel by default, serial when dependent
- Idempotent tasks enable reruns

### Operators Do the Work
- Use official operators when available
- PythonOperator for custom logic
- TaskFlow API for cleaner code

---

## Common Patterns

### Modern DAG (TaskFlow API)
```python
from datetime import datetime
from airflow.decorators import dag, task

@dag(
    dag_id='etl_pipeline',
    schedule='@daily',
    start_date=datetime(2024, 1, 1),
    catchup=False,
    tags=['etl', 'production'],
)
def etl_pipeline():
    
    @task()
    def extract():
        return {"data": "extracted_data"}
    
    @task()
    def transform(data: dict):
        return {"transformed": data["data"].upper()}
    
    @task()
    def load(data: dict):
        print(f"Loading: {data}")
    
    # Define dependencies
    raw_data = extract()
    transformed_data = transform(raw_data)
    load(transformed_data)

etl_pipeline()
```

### Spark on Kubernetes
```python
from airflow.providers.cncf.kubernetes.operators.spark_kubernetes import SparkKubernetesOperator

spark_job = SparkKubernetesOperator(
    task_id='spark_etl',
    namespace='data',
    application_file='spark-job.yaml',
    kubernetes_conn_id='kubernetes_default',
)
```

### dbt Integration
```python
from airflow.operators.bash import BashOperator

dbt_run = BashOperator(
    task_id='dbt_run',
    bash_command='cd /dbt && dbt run --profiles-dir .',
)

dbt_test = BashOperator(
    task_id='dbt_test',
    bash_command='cd /dbt && dbt test --profiles-dir .',
)

dbt_run >> dbt_test
```

---

## Anti-Patterns

| Anti-Pattern | Solution |
|--------------|----------|
| Heavy logic in DAG file | Move to external scripts/modules |
| No retries configured | Set `retries=2, retry_delay=timedelta(minutes=5)` |
| Hardcoded connections | Use Airflow connections/variables |

---

## Related Skills

- For Spark: `spark-patterns`
- For dbt: `dbt-patterns`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cemalcici) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
