# Network Traffic Forecasting for 5G/6G and IoT Networks

A leakage-aware forecasting research workflow that predicts **network bytes transferred one second ahead** from packet-level traffic.

> This repository is an **experimental/research artifact** (not a production deployment). Azure ML deployment was intentionally **not started** in this work.

## Why this project matters

In 5G/6G and IoT operations, traffic can spike quickly. Reliable one-second-ahead forecasts can support:
- short-horizon capacity planning,
- anomaly triage and congestion prevention,
- autoscaling and policy decisions on near-real-time telemetry.

This project demonstrates an end-to-end, evidence-driven workflow that prioritizes leakage control and honest evaluation on chronological splits.

## Project highlights

- Built a complete packet-to-forecast pipeline in the notebook [`network_traffic_for_5G-6G_&_IoT_networks_research_workflow (1).ipynb`](./network_traffic_for_5G-6G_%26_IoT_networks_research_workflow%20%281%29.ipynb)
- Supports loading packet data from either local CSV or Snowflake
- Performs timestamp/data-quality validation before modeling
- Aggregates packet traffic into one-second intervals
- Engineers lag, shifted rolling, and calendar features with leakage controls
- Compares baseline, linear, tree, and RNN models on aligned chronological test rows
- Tracks experiments with MLflow and exports reproducible artifacts/reports

## End-to-end workflow

1. **Data ingestion**
   - Loads packet-level rows from Snowflake or local CSV.
2. **Data validation and quality checks**
   - Parses timestamps, checks invalid/duplicate/missing patterns, and flags protocol anomalies.
3. **One-second aggregation**
   - Builds interval-level traffic features from packet records.
4. **Leakage-aware feature engineering**
   - Uses lag features and rolling statistics computed from shifted series (`shift(1)`).
   - Defines target as `TARGET_T_PLUS_1`.
5. **Chronological splitting**
   - Uses 70/15/15 train/validation/test order-preserving split.
6. **Model training and comparison**
   - Trains classical ML models and recurrent neural networks.
7. **Evaluation and export**
   - Compares models on a shared timestamp-aligned test slice.
   - Exports metrics, predictions, model/scaler artifacts, and machine-readable summary.

## Forecasting-time assumption (important)

Current closed-interval aggregate features (at time *t*) are valid predictors only when inference is performed **after interval *t* closes**, to predict *t+1*. They are not valid for earlier-in-interval forecasting.

## Dataset facts (from repository evidence)

- Raw packet rows: **6,095,138**
- Aggregated one-second intervals: **21,145**
- Temporal coverage: **~5 hours 52 minutes** (`0 days 05:52:24.332498`)
- Invalid timestamps: **0**
- Missing raw values: **0**
- Unexpected protocol rows: **2,539**
- Common evaluation rows: **3,163**

Evidence sources:
- [`research_report.json`](./research_report.json)
- [`FINAL_RESEARCH_SUMMARY.md`](./FINAL_RESEARCH_SUMMARY.md)
- [`final_model_comparison.csv`](./final_model_comparison.csv)

## Model results (common evaluation rows = 3,163)

| Model | Family | MAE | RMSE | R² | MAPE |
|---|---|---:|---:|---:|---:|
| HistGradientBoosting | Tree | 10,966.631 | 15,660.102 | 0.726783 | 5.377 |
| RandomForest | Tree | 11,017.757 | 15,726.165 | 0.724473 | 5.372 |
| GradientBoosting | Tree | 11,033.377 | 15,731.195 | 0.724296 | 5.432 |
| Lasso | Linear | 11,283.100 | 16,145.135 | 0.709596 | 5.652 |
| NaivePersistence | Baseline | 11,355.868 | 16,982.810 | 0.678680 | 5.495 |
| DecisionTree | Tree | 11,975.239 | 17,416.365 | 0.662064 | 5.846 |
| GRU | RNN | 12,001.885 | 17,062.012 | 0.675676 | 6.138 |
| Ridge | Linear | 13,559.795 | 18,609.428 | 0.614180 | 6.888 |
| LinearRegression | Linear | 14,009.115 | 19,012.484 | 0.597286 | 7.108 |
| LSTM | RNN | 16,621.841 | 20,670.230 | 0.523997 | 8.204 |

Current selected model: **HistGradientBoosting** (MAE ≈ **10,966.631 bytes**).

## Reproducibility

### 1) Environment setup

```bash
pip install -r requirements_network_traffic.txt
```

### 2) Data source configuration

The notebook supports:
- **Local CSV** (default code path in notebook; update the CSV path in the data-loading cell as needed)
- **Snowflake** via environment variables:
  - `SNOWFLAKE_USER`
  - `SNOWFLAKE_PASSWORD`
  - `SNOWFLAKE_ACCOUNT`
  - Optional overrides: `SNOWFLAKE_WAREHOUSE`, `SNOWFLAKE_DATABASE`, `SNOWFLAKE_SCHEMA`

### 3) Run workflow

Open and run, in order:
- [`network_traffic_for_5G-6G_&_IoT_networks_research_workflow (1).ipynb`](./network_traffic_for_5G-6G_%26_IoT_networks_research_workflow%20%281%29.ipynb)

### 4) Review outputs committed in this repository

- [`final_model_comparison.csv`](./final_model_comparison.csv)
- [`common_timestamp_predictions.csv`](./common_timestamp_predictions.csv)
- [`research_report.json`](./research_report.json)
- [`FINAL_RESEARCH_SUMMARY.md`](./FINAL_RESEARCH_SUMMARY.md)
- [`histgradientboosting_model.pkl`](./histgradientboosting_model.pkl)
- [`gru_model.keras`](./gru_model.keras)
- [`lstm_model.keras`](./lstm_model.keras)
- [`feature_columns.pkl`](./feature_columns.pkl)
- [`gru_feature_scaler.pkl`](./gru_feature_scaler.pkl)
- [`gru_target_scaler.pkl`](./gru_target_scaler.pkl)
- [`lstm_feature_scaler.pkl`](./lstm_feature_scaler.pkl)
- [`lstm_target_scaler.pkl`](./lstm_target_scaler.pkl)
- [`mlflow.db`](./mlflow.db)

## Technology stack

- Python, Jupyter Notebook
- pandas, NumPy
- scikit-learn
- TensorFlow/Keras (LSTM/GRU)
- MLflow
- Snowflake Connector for Python
- matplotlib, seaborn

Dependencies are listed in [`requirements_network_traffic.txt`](./requirements_network_traffic.txt).

## Project structure

- Canonical overview: [`README.md`](./README.md)
- Main workflow notebook: [`network_traffic_for_5G-6G_&_IoT_networks_research_workflow (1).ipynb`](./network_traffic_for_5G-6G_%26_IoT_networks_research_workflow%20%281%29.ipynb)
- Supporting evidence snapshot: [`README_network_traffic_forecasting.md`](./README_network_traffic_forecasting.md)
- Final report snapshot: [`FINAL_RESEARCH_SUMMARY.md`](./FINAL_RESEARCH_SUMMARY.md)
- Machine-readable summary: [`research_report.json`](./research_report.json)
- Metrics/predictions/artifacts: root-level CSV/PKL/KERAS files listed above

## Potential performance issues and optimization opportunities

### Observed bottlenecks in the current notebook workflow

- Loading the full packet table (~6.1M rows) into pandas shows roughly **1.9 GB** memory usage.
- Multiple stages repeatedly perform full DataFrame `copy()` and `sort_values()` operations.
- Several columns begin as `object`/string types (for example timestamp/protocol/source fields), increasing memory and processing overhead.
- One-second `resample`/groupby-style aggregations over millions of packet rows are computationally expensive.
- The workflow trains many models end-to-end; repeated training adds wall-clock cost.
- RNN training (GRU/LSTM) is materially slower than classical tree/linear baselines.
- Notebook plotting/display-heavy cells can add substantial interactive overhead.
- MLflow tracking to local SQLite (`mlflow.db`) is fine for experiments but can become a bottleneck for scaling/concurrency.
- Artifact handling is notebook-centric; reproducibility depends on careful path/environment consistency.

### Recommended optimization opportunities (not benchmarked here)

- Use selective column loading and explicit dtypes (`category`, narrower numeric types where safe).
- Minimize intermediate copies; consolidate transforms and avoid unnecessary full re-sorts.
- Push heavy pre-aggregation to Snowflake when available.
- Cache/reuse intermediate aggregated/feature datasets between notebook runs.
- Narrow model search or use staged evaluation to reduce repeated full retraining.
- Reduce plotting frequency or gate expensive visualizations behind flags.
- Move MLflow tracking backend/artifacts to a more scalable remote store if collaboration grows.
- Add a scripted, deterministic pipeline entry point (in addition to notebook) for repeatable batch reruns.

## Limitations and caveats

- Data represents a **single capture window (~5h52m)**.
- Results are specific to current one-second aggregation, feature design, and hyperparameters.
- Broader temporal and cross-environment validation is still required before production claims.
- This repository documents a research workflow; production deployment hardening was out of scope.

## Future improvements

- Validate across multiple days/capture windows and heterogeneous network conditions.
- Add robust backtesting and drift monitoring slices.
- Tune and regularize models with reproducible experiment sweeps.
- Convert notebook logic into modular pipeline code with automated tests.
- Implement production-grade serving/monitoring only after broader validation.
