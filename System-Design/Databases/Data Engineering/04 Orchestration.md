---
tags: [data-engineering, orchestration, airflow, dagster, prefect]
---

# 04 — Orchestration

## Airflow DAG Example

```python
from airflow import DAG
from airflow.operators.bash import BashOperator
from airflow.providers.dbt.cloud.operators.dbt import DbtCloudRunJobOperator
from datetime import datetime, timedelta

default_args = {
    'owner': 'data_engineering',
    'retries': 2,
    'retry_delay': timedelta(minutes=5),
}

with DAG(
    dag_id='sales_analytics_pipeline',
    default_args=default_args,
    schedule_interval='0 6 * * *',
    start_date=datetime(2024, 1, 1),
    catchup=False,
    tags=['sales', 'production'],
) as dag:
    extract = BashOperator(task_id='extract_orders',
        bash_command='python /scripts/extract_orders.py --date {{ ds }}')
    validate = BashOperator(task_id='validate_data',
        bash_command='echo "Validation passed"')
    run_dbt = DbtCloudRunJobOperator(task_id='run_dbt_models',
        dbt_cloud_conn_id='dbt_cloud', job_id=12345)
    quality = BashOperator(task_id='run_quality_checks',
        bash_command='dbt test --select tag:production')
    load = BashOperator(task_id='load_reporting',
        bash_command='python /scripts/refresh_materialized_views.py')
    notify = BashOperator(task_id='notify_slack',
        bash_command='python /scripts/slack_notify.py --status success')

    extract >> validate >> run_dbt >> quality >> load >> notify
```

## Dagster vs Prefect vs Airflow

| Feature | Airflow | Dagster | Prefect |
|---------|---------|---------|---------|
| Paradigm | DAG as code | Software-defined assets | Flow/Deployment |
| Asset lineage | Implicit (XComs) | First-class (asset graph) | Manual |
| Data quality | Custom operators | Asset checks (built-in) | Custom checks |
| Local dev | Hard | Great (dagster dev) | Great |
| Backfilling | Complex (catchup) | Simple (partitioned assets) | Simple |
| UI | Mature | Excellent (Dagit) | Clean |
| Testing | Hard | Great | Good |
| Learning curve | Steep | Moderate | Low |
| Best for | Large enterprise | Data platform teams | Startups, teams migrating |

**Links**: [[System-Design/Databases/Data Engineering/03 dbt]] | [[System-Design/Databases/Data Engineering/05 Data Quality & Cataloging]] | [[System-Design/Databases/Data Engineering/07 Real-Time Streaming]]
**See also**: [[Apache Airflow]], [[Apache Spark]]
