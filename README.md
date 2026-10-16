# meuingles-data-platform

Data platform of [MeuInglês](https://meuingles.wellgois.com/), a spoken-English trainer for data engineers.

**The code lives in the app repository**, next to the database it reads, in
[`meuingles-app/data-platform`](https://github.com/wellgois/meuingles-app/tree/main/data-platform).
This repository only points there, so there is one source of truth.

## What is built (and what is not)

| Layer | Status |
|---|---|
| Extract from PostgreSQL, pseudonymized Parquet (HMAC-SHA256) | Scheduled daily with cron |
| Upload to ADLS Gen2 landing (write-only SAS, 30-day retention) | Scheduled daily with cron |
| Bronze and silver (Delta Lake, PySpark, idempotent `MERGE`) | Implemented, tested in CI on local Spark; Databricks job not provisioned yet |
| Gold (dbt, 6 models, 17 data tests) | Runs on DuckDB in CI; Databricks target prepared, not exercised yet |
| Orchestration | cron in production; Airflow DAG and Prefect flow tested in CI, not deployed |
| Azure Data Factory | Not provisioned for this project |

## Architecture

```
PostgreSQL -> extractor (Parquet, pseudonymized) -> ADLS Gen2 landing
           -> bronze (Delta) -> silver (Delta, MERGE) -> gold (dbt)
           -> public panel (static HTML, DuckDB)
```

## Where to start reading

- [`data-platform/README.md`](https://github.com/wellgois/meuingles-app/blob/main/data-platform/README.md): architecture, design decisions, privacy, cost control.
- [`data-platform/dbt`](https://github.com/wellgois/meuingles-app/tree/main/data-platform/dbt): gold models and tests.
- [`data-platform/orchestration`](https://github.com/wellgois/meuingles-app/tree/main/data-platform/orchestration): Airflow DAG and Prefect flow.

Related: the same author's [JogandoLógica data platform](https://github.com/wellgois/data-exports) (Azure Data Factory, Databricks, medallion).
