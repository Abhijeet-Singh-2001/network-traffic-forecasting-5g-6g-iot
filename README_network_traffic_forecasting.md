# Network Traffic Forecasting for 5G/6G and IoT Networks

An evidence-based, leakage-aware forecasting pipeline for predicting one-second-ahead network traffic volume from packet-level data.

## Outcome

The corrected experiment evaluates ten models on the same 3,163 chronological test timestamps. HistGradientBoosting is currently selected by MAE.

| Model | Family | MAE | RMSE | R2 | MAPE |
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

These results are specific to the current capture window, one-second aggregation, feature set, and hyperparameters.

## Research design

- Raw data: 6,095,138 packet rows.
- Aggregation: one-second intervals.
- Target: `BYTES_TRANSFERRED` at `t+1`.
- Split: chronological 70% train, 15% validation, 15% test.
- Features: historical lags, shifted rolling statistics, current closed-interval aggregates, and calendar variables.
- Leakage control: target is `TARGET_T_PLUS_1`; rolling features use `shift(1)`; scalers fit on training data only.
- Missing values: raw data contains zero missing cells.
- Outliers: valid extreme traffic is retained; Isolation Forest is exploratory, not an automatic deletion rule.
- MLflow: 10 model runs logged.
- Azure ML: intentionally not started.

## Forecasting assumption

Current one-second aggregate features are valid predictors when the model is called after the current interval closes and predicts the next interval. They would be invalid for an earlier-in-interval forecast and must then be replaced by lagged-only features.

## Files

- `network_traffic_for_5G-6G_&_IoT_networks_research_workflow (1).ipynb`: research notebook.
- `network_traffic_outputs/final_model_comparison.csv`: final metrics.
- `network_traffic_outputs/common_timestamp_predictions.csv`: aligned predictions.
- `network_traffic_outputs/research_report.json`: machine-readable summary.
- `network_traffic_outputs/FINAL_RESEARCH_SUMMARY.md`: presentation-ready report.
- `network_traffic_outputs/histgradientboosting_model.pkl`: selected model artifact.
- `network_traffic_outputs/gru_model.keras`: GRU artifact.
- `network_traffic_outputs/lstm_model.keras`: LSTM artifact.

## Reproduce

1. Install dependencies from `requirements_network_traffic.txt`.
2. Place the local dataset at `Downloads/Zoom_1.csv/Zoom_1.csv`, or configure Snowflake environment variables.
3. Open the notebook in VS Code.
4. Run cells in order.
5. Review `network_traffic_outputs/FINAL_RESEARCH_SUMMARY.md` and the final comparison CSV.

## Important limitation

The current data covers approximately 5 hours and 52 minutes from one capture window. Broader temporal validation across multiple days or captures is required before production deployment or strong generalization claims.
