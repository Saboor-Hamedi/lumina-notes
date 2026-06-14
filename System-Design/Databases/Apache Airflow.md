---
id: a1b2c3d4-1158-4000-8000-000000000158
title: Apache Airflow
language: markdown
tags: [system-design, databases, airflow, workflow]
selection: null
isPinned: false
timestamp: 1781500001158
---

**Links**: [[ETL and Data Pipeline Patterns]] | [[Data Engineering]] | [[Stream Processing]] | [[Apache Spark]] | [[Data Warehouse Modeling]] | [[Delta Lake and Apache Iceberg]]


# Apache Airflow

Airflow is a platform for programmatically authoring, scheduling, and monitoring workflows. DAGs (Directed Acyclic Graphs) define pipeline structure.

## Core Concepts

| Concept | Description |
|---------|-------------|
| DAG | Directed Acyclic Graph — defines workflow structure |
| Task | A single unit of work in a DAG |
| Operator | Template for a task (PythonOperator, BashOperator) |
| Sensor | Special operator that waits for an event |
| Scheduler | Triggers DAGs based on schedule |
| Executor | Runs tasks (Sequential, Local, Celery, Kubernetes) |
| Worker | Process that executes tasks |

## DAG Definition

```python
from datetime import datetime, timedelta
from airflow import DAG
from airflow.operators.python import PythonOperator
from airflow.operators.bash import BashOperator

default_args = {
    "owner": "data_team",
    "depends_on_past": False,
    "email_on_failure": True,
    "retries": 3,
    "retry_delay": timedelta(minutes=5),
}

with DAG(
    dag_id="daily_etl",
    default_args=default_args,
    description="Daily ETL pipeline",
    schedule="0 6 * * *",       # Cron expression: 6 AM daily
    start_date=datetime(2026, 1, 1),
    catchup=True,                # Backfill missed runs
    tags=["etl", "daily"],
    max_active_runs=1,
) as dag:

    def extract(**context):
        execution_date = context["execution_date"]
        # Extract data...
        return {"records_extracted": 1000, "source": "postgres"}

    def transform(ti, **context):
        extracted = ti.xcom_pull(task_ids="extract")
        # Transform data...
        return {"records_transformed": extracted["records_extracted"]}

    def load(ti, **context):
        transformed = ti.xcom_pull(task_ids="transform")
        # Load data...
        pass

    extract_task = PythonOperator(
        task_id="extract",
        python_callable=extract,
        provide_context=True,
    )

    transform_task = PythonOperator(
        task_id="transform",
        python_callable=transform,
        provide_context=True,
    )

    load_task = PythonOperator(
        task_id="load",
        python_callable=load,
        provide_context=True,
    )

    # Dependencies
    extract_task >> transform_task >> load_task
```

## Task Dependencies

```python
# Linear
task1 >> task2 >> task3

# Fan-out (parallel)
task1 >> [task2, task3, task4]

# Fan-in
[task1, task2] >> task3

# Conditional branching (using BranchPythonOperator)
def choose_branch(**context):
    if context["execution_date"].day == 1:
        return "monthly_report"
    return "daily_report"

branch = BranchPythonOperator(task_id="branch", python_callable=choose_branch)
branch >> [daily_report, monthly_report]
```

## Operators

```python
from airflow.providers.postgres.operators.postgres import PostgresOperator
from airflow.providers.amazon.aws.transfers.s3_to_redshift import S3ToRedshiftOperator
from airflow.providers.slack.operators.slack_webhook import SlackWebhookOperator
from airflow.sensors.external_task import ExternalTaskSensor

# SQL execution
create_table = PostgresOperator(
    task_id="create_table",
    postgres_conn_id="data_warehouse",
    sql="""
        CREATE TABLE IF NOT EXISTS daily_metrics (
            date DATE,
            metric_name VARCHAR(100),
            metric_value DECIMAL(20,2)
        );
    """,
)

# File transfer
load_to_redshift = S3ToRedshiftOperator(
    task_id="load_to_redshift",
    s3_bucket="data-lake",
    s3_key="daily_metrics/{{ ds }}/*.parquet",
    schema="public",
    table="daily_metrics",
    copy_options=["FORMAT AS PARQUET"],
)

# Wait for upstream pipeline
wait_for_source = ExternalTaskSensor(
    task_id="wait_for_source_dag",
    external_dag_id="source_extract",
    external_task_id="complete",
    timeout=3600,
    poke_interval=60,
)

# Notification
notify = SlackWebhookOperator(
    task_id="notify",
    slack_webhook_conn_id="slack_etl",
    message="Daily ETL completed successfully",
)
```

## Scheduling and Catchup

```python
# Schedule: cron or timedelta
schedule="0 6 * * *"         # Daily at 6 AM
schedule=timedelta(hours=6)  # Every 6 hours

# Catchup: runs all missed intervals from start_date
catchup=True   # Will backfill missed runs
catchup=False  # Only run the latest

# If start_date is 2026-01-01 and today is 2026-06-13:
# With catchup=True → 163 DAG runs
# With catchup=False → 1 DAG run (today or latest)
```

## XCom (Cross-Communication)

```python
# Push data
def push_task(ti):
    ti.xcom_push(key="user_count", value=5000)

# Pull data
def pull_task(ti):
    count = ti.xcom_pull(task_ids="push_task", key="user_count")

# XCom is stored in the database — use for small data (< 1MB) only
```

## Connections and Variables

```bash
# CLI
airflow connections add 'data_warehouse' \
    --conn-type 'postgres' \
    --conn-host 'myhost' \
    --conn-login 'user' \
    --conn-password 'pass' \
    --conn-port 5432

airflow variables set 'environment' 'production'
```

```python
from airflow.models.connection import Connection
from airflow.models.variable import Variable

env = Variable.get("environment", default_var="dev")
conn_info = Connection.get_connection_from_secrets("data_warehouse")
```

## Executors

| Executor | Scale | Use Case |
|----------|-------|----------|
| SequentialExecutor | Single machine | Development/testing |
| LocalExecutor | Single machine, parallel | Small to medium |
| CeleryExecutor | Distributed | Production (most common) |
| KubernetesExecutor | Dynamic pods | Cloud-native, elastic |
| LocalKubernetesExecutor | Hybrid | Mixed workloads |

## Monitoring

```python
# SLA monitoring
with DAG(...) as dag:
    task = PythonOperator(
        task_id="critical_task",
        python_callable=my_func,
        sla=timedelta(hours=2),  # Alert if task runs > 2 hours
    )

# SLAs are checked after the DAG run completes
# Failed SLA → email alert + SLA miss record in database
```

## Best Practices

```python
# 1. Idempotent tasks — re-runnable without side effects
# 2. Small, focused tasks — one logical operation per task
# 3. Push large data to object store, not XCom
# 4. Set retries and retry_delay for transient failures
# 5. Use task tags for organization
# 6. Test DAGs with dag.test() before deploying
# 7. Set depends_on_past=False unless strictly required
# 8. Use @task decorator for simple Python tasks

from airflow.decorators import dag, task

@dag(schedule="@daily", start_date=datetime(2026, 1, 1), catchup=False)
def simplified_etl():
    @task
    def extract():
        return {"data": [1, 2, 3]}

    @task
    def transform(data):
        return [x * 2 for x in data["data"]]

    @task
    def load(data):
        print(f"Loading: {data}")

    load(transform(extract()))

dag = simplified_etl()
```
