# Supporting Evidence Snapshot: Network Traffic Forecasting

This file is retained as a **supporting evidence snapshot**.

For the canonical, recruiter-facing project documentation, use:
- [`README.md`](./README.md)

## Snapshot highlights

- Evaluates ten models on the same 3,163 chronological test timestamps.
- Current selected model by MAE: **HistGradientBoosting** (MAE: **10,966.631**).
- Results are specific to the current capture window, one-second aggregation, feature set, and hyperparameters.
- Azure ML deployment was **not started**.

## Supporting files (actual repository paths)

- [`network_traffic_for_5G-6G_&_IoT_networks_research_workflow (1).ipynb`](./network_traffic_for_5G-6G_%26_IoT_networks_research_workflow%20%281%29.ipynb)
- [`final_model_comparison.csv`](./final_model_comparison.csv)
- [`common_timestamp_predictions.csv`](./common_timestamp_predictions.csv)
- [`research_report.json`](./research_report.json)
- [`FINAL_RESEARCH_SUMMARY.md`](./FINAL_RESEARCH_SUMMARY.md)
- [`histgradientboosting_model.pkl`](./histgradientboosting_model.pkl)
- [`gru_model.keras`](./gru_model.keras)
- [`lstm_model.keras`](./lstm_model.keras)

## Limitation reminder

The current dataset covers approximately **5 hours 52 minutes** from one capture window. Broader temporal validation is required before production deployment claims.
