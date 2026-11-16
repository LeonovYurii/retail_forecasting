# Retail forecasting laboratory

## Goal
Refresh classical forecasting, compare modern ML and neural methods, and deliver a
reproducible GitHub portfolio. The final solution must support the entire competition
dataset and generate a complete valid Kaggle submission; sample analysis is stage one.
Source: https://www.kaggle.com/competitions/store-sales-time-series-forecasting

## Initial slice
Default: 6 stores total, 3 in each of 2 states, with the SAME 5 product families in
every store: 30 store-family series. Five stores would mean 25 series if needed.
The dataset contains product families, not individual SKU-level products.
Choose actual IDs/names after inspecting stores.csv and training history. Do not
invent names or silently fall back to all data when selection lists are empty.

Use geography state -> city -> store (city keyed together with state). Cluster and
store type are alternative store attributes, NOT levels beneath state in a strict
geographic hierarchy. Retain both for comparisons and grouped summaries. Cluster
labels describe groups of similar stores and may cross state boundaries.

Selection procedure to implement later:
1. Inspect stores.csv and check its store key is unique.
2. Select 2 states with at least 3 eligible stores each; retain variation in city,
   type and cluster when feasible. Use history available before the first validation
   cutoff to assess coverage and activity; do not select using holdout performance.
3. Select 5 shared families with useful histories. Include differing demand patterns;
   document bias if excluding sparse families. Do not require every observation > 0.
4. Freeze state/store/family lists in sample.toml and save a selection manifest with
   reasons, selection cutoff, coverage, missing dates and zero shares.
5. Preserve all historical dates for those series; use a smaller training window only
   as an explicit configuration. Keep a clear report of any unavailable combinations.

## Data representation
Long observation table: date, store_nbr, family, sales, onpromotion.
Primary key: (date, store_nbr, family); stable series ID from (store_nbr, family).
Store dimension: store_nbr, state, city, type, cluster.
Calendar/holiday data: keep geographic scope and transferred-holiday semantics.
Store transactions: store-date grain; avoid duplication after joining families.
Oil: date grain; handle missing dates explicitly.
Validate many-to-one joins and row counts; never silently multiply sales rows.
Library adapters may expose unique_id/ds/y; retain source identifiers for submissions.

## Analysis and aggregation
Compare store-family, store, state-family, cluster-family and overall sample patterns.
Any aggregate in the sample is a SELECTED-family/SELECTED-store subtotal, not total
state sales. Check units before summing different families; unit quantities and weight
quantities are not interchangeable, and these sales are not revenue.
Missing dates, zero sales, closures and pre-opening periods need separate treatment.
Do not automatically interpolate or delete anomalies. Inspect business explanations.
Geography and cluster groupings overlap: do not double-count them in reconciliation.

## Validation and models
Use the competition's 16-day forecast horizon and several rolling origins. Use the
same cutoffs and scored observations across models. Record per-series and aggregate
metrics (RMSLE for competition alignment, MAE for interpretation) and fit/predict time.
Report the exact averaging/weighting scheme. Handle nonnegative predictions consistently.
Fit transformations/anomaly models only on training windows. Lag/rolling features
must not use targets inside the forecast horizon. Recursive models use their own
predictions for later forecast steps; distinguish this from one-step evaluation.
Use only covariates available at forecast time. Calendar is known; promotion plans may
be supplied. Future transactions/oil cannot simply be taken from validation history.

Stages: seasonal-naive baselines -> statsmodels ETS/SARIMAX -> pmdarima/StatsForecast
-> MLForecast with LightGBM -> NeuralForecast N-HiTS -> focused Darts comparison ->
STUMPY/PyOD anomaly experiments. Libraries are optional per experiment, not all required
for every run. Scale per-series models only after measuring runtime on the sample.

## Full-data completion criteria
The same loading, preparation, validation, training and prediction code takes either
sample.toml or full.toml. Full mode includes all stores/families required by test.csv.
Use batching/partitioned data when needed; sample success is not evidence full mode runs.
Verify coverage, uniqueness, ordering by submission ID, finite nonnegative predictions,
and exact required columns against sample_submission.csv. Do not publish fake scores.
Produce a full-data benchmark, submission CSV (local artifact), curated results,
limitations and reproduction instructions. GitHub contains code/docs/results, not raw data.

## Current stage
Scaffold only. No series selected, data loaded, or models trained. First notebook:
01_data_dimensions_and_sample.ipynb, created after data is available. Start with stores,
families and calendar coverage before modelling.
