# Freight Rate Prediction
Freight rate prediction using feature engineering and LightGBM regression.
1. Project Overview

This project focuses on predicting freight rates for transportation loads using historical shipment data and engineered features.

The objective is to build a machine learning model that estimates the expected freight rate based on shipment locations, distance, equipment type, weight, market conditions, and quote signals.

The final solution uses LightGBM with a log-transformed target, trained on historical data and evaluated using a chronological validation split.

2. Dataset

The project uses the following datasets:

Dataset

Description

Rows

train_test.csv

Historical labelled shipment data

48,000

validation.csv

Loads requiring rate predictions

12,000

validation_predictions.csv

Final predictions for validation loads

12,000

december_chart_inputs.csv

December scenario inputs with predicted rates

31

The target variable is posted_rate.

3. Data Preprocessing and Feature Engineering

Several features were engineered to capture geographical, temporal, and shipment-related patterns.

Geographical Features

Haversine-based geographical distance between pickup and delivery coordinates.

Ratio of provided distance to geographical distance.

Latitude and longitude differences.

Temporal Features

Month

Day of week

Day of month

Day of year

Weekend indicator

Distance and Signal Features

Log-transformed distance (log_distance)

Square-root distance (sqrt_distance)

Squared quote signal (quote_signal_squared)

Missing-Value Indicators

weight_missing

market_index_missing

weight_negative

Categorical features such as pickup, delivery, and equipment were handled using LightGBM categorical features.

4. Train-Validation Strategy

A chronological split was used to simulate real-world prediction conditions and avoid future-data leakage.

Split

Period

Samples

Training

January – August 2025

38,477

Validation

September – October 2025

9,523

After model selection, the final model was retrained using all 48,000 labelled training records.

5. Model Selection

Multiple modelling approaches were evaluated:

Model

Validation MAE

CatBoost (raw target)

$114.85

CatBoost (log target)

$112.97

LightGBM (log target)

$109.51

LightGBM with a log-transformed target achieved the lowest validation MAE and was selected as the final model.

Final Model Configuration

Algorithm: LightGBM Regressor

Objective: regression_l1

Target transformation: log1p(posted_rate)

Learning rate: 0.03

Number of leaves: 31

Maximum estimators: 3000

Selected iterations: 831

Feature fraction: 0.8

Subsample: 0.8

L1 regularization: 0.1

L2 regularization: 1.0

Random state: 42

The model predictions are converted back to the original rate scale using expm1.

6. Validation Results

The selected LightGBM model achieved:

Metric

Result

Mean Absolute Error (MAE)

$109.51

Root Mean Squared Error (RMSE)

$635.11

The final model was retrained on the complete labelled dataset before generating the 12,000 validation predictions.

7. December Scenario Predictions

The December scenario contains 31 daily shipment inputs for the Lexington-to-Fort Wayne route.

Since the December inputs did not contain all model features, missing geographical coordinates were obtained from historical training records, while market index and quote signal were populated using historical route-level medians.

The final model generated predictions for all 31 dates.

December 1 predicted rate: approximately $828.18

December 31 predicted rate: approximately $838.58

The generated chart is available at candidate_december.png.
