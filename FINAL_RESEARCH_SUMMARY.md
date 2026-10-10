# Network Traffic Forecasting: Final Research Summary

## Objective
Predict one-second-ahead `BYTES_TRANSFERRED` from packet-level 5G/6G and IoT network traffic.

## Dataset
- Raw observations: 6,095,138
- Aggregated one-second intervals: 21,145
- Temporal coverage: 0 days 05:52:24.332498
- Invalid timestamps: 0
- Raw missing cells: 0

## Experimental design
- Chronological split: 70% train, 15% validation, 15% test.
- Forecast horizon: one second.
- Common evaluation rows: 3,163.
- Current aggregate features are allowed because this is an operational t+1 forecast after interval t closes.
- No same-timestamp target is included in the predictors.

## Final comparison

|     MAE |    RMSE |       R2 |    MAPE | pct_metric_name   | Model                | ModelFamily   |   TrainingTimeSec |   NumParameters |   CommonEvaluationRows |
|--------:|--------:|---------:|--------:|:------------------|:---------------------|:--------------|------------------:|----------------:|-----------------------:|
| 10966.6 | 15660.1 | 0.726783 | 5.37695 | MAPE              | HistGradientBoosting | Tree          |         0.393922  |             nan |                   3163 |
| 11017.8 | 15726.2 | 0.724473 | 5.37208 | MAPE              | RandomForest         | Tree          |         4.6001    |             nan |                   3163 |
| 11033.4 | 15731.2 | 0.724296 | 5.43201 | MAPE              | GradientBoosting     | Tree          |         9.33391   |             nan |                   3163 |
| 11283.1 | 16145.1 | 0.709596 | 5.65222 | MAPE              | Lasso                | Linear        |        10.3111    |             nan |                   3163 |
| 11355.9 | 16982.8 | 0.67868  | 5.49508 | MAPE              | NaivePersistence     | Baseline      |       nan         |             nan |                   3163 |
| 11975.2 | 17416.4 | 0.662064 | 5.84605 | MAPE              | DecisionTree         | Tree          |         0.298771  |             nan |                   3163 |
| 12001.9 | 17062   | 0.675676 | 6.13842 | MAPE              | GRU                  | RNN           |        67.0321    |           24609 |                   3163 |
| 13559.8 | 18609.4 | 0.61418  | 6.88842 | MAPE              | Ridge                | Linear        |         0.0533692 |             nan |                   3163 |
| 14009.1 | 19012.5 | 0.597286 | 7.10769 | MAPE              | LinearRegression     | Linear        |         0.0676157 |             nan |                   3163 |
| 16621.8 | 20670.2 | 0.523997 | 8.20441 | MAPE              | LSTM                 | RNN           |        61.9028    |           32417 |                   3163 |

## Current outcome
- Selected model by MAE: **HistGradientBoosting**
- Selected-model MAE: **10,966.631 bytes**
- MLflow runs logged: **10**
- Verified output artifacts: **11**
- Azure ML deployment: **not started**

## Interpretation
The corrected one-step-ahead experiment does not support the earlier near-perfect same-time results. HistGradientBoosting currently provides the best observed error, while GRU and LSTM do not justify their added complexity on this dataset under the current configuration.

This conclusion remains specific to the current data window, one-second aggregation, feature availability assumption, and hyperparameters. A broader time-period evaluation is required before production deployment.
