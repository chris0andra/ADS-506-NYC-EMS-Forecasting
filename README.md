# NYC Emergency Medical Services Demand and Response-Time Forecasting

**ADS-506: Applied Time-Series Analysis · University of San Diego · Fall 2026**  
**Project team:** Christopher Andra, Daniel Lopez, and Danitza Loya

> **Branch-specific documentation:** This README describes the additional tuning and validation work on Christopher Andra's development branch (`Data-Cleaning-&-Time-Series-EDA`). These model selections and forecasts should not be assumed to represent the team's `main` branch or final presentation unless the team adopts them.

## Business Question and Purpose

**How can NYC EMS use historical data to forecast future emergency incident demand and EMS response times?**

The objective is to forecast weekly incident counts and average **call-to-arrival time** for life-threatening and non-life-threatening EMS incidents. These estimates may help leadership anticipate workload and recognize potential response-time pressures when discussing staffing, coverage, and operational readiness. This is a forecasting project, **not** a causal study of delays or an optimization model that determines exact staffing levels.

## Data

- **Source:** [NYC Open Data — 911 End-to-End Data](https://data.cityofnewyork.us/Public-Safety/911-End-to-End-Data/t7p9-n9dy)
- **Scope:** NYC emergency medical services (EMS) records, aggregated to regular weekly observations.
- **Historical period:** November 18, 2013–September 14, 2026 (**670 weeks**).
- **Categories:** Life-Threatening and Non-Life-Threatening.
- **Primary fields:** `Week`, `Category`, `Incidents`, `Arrival_Seconds`, `Dispatch_Minutes`, `Travel_Minutes`, and `Total_Incidents`.
- **Train/test split:** First **618 weeks** for training (through September 15, 2025), followed by a **52-week holdout** (September 22, 2025–September 14, 2026).

**Important demand definition:** The *classified total* forecast is built from the two classified categories. The source's overall `Total_Incidents` includes 23 incidents without those classifications, so it is not identical to classified demand. Independently fitted total and category forecasts are also **not mathematically reconciled** and need not sum exactly.

## Analysis Workflow

1. **Data preparation and exploration:** Clean and organize weekly EMS observations; visualize incident volumes and call-to-arrival times; examine trends, seasonality, unusual periods, and decomposition.
2. **Initial forecasting:** Evaluate ARIMA and exponential smoothing (ETS) models, including forecast errors and residual diagnostics; preserve the original 52-week holdout evaluation as a distinct analysis.
3. **Additional model search on this branch:** Systematically evaluate **9 ARIMA configurations** (`p = 0, 1, 2`; `d = 1`; `q = 0, 1, 2`) and **7 ETS configurations** (six specified structures plus automatic ETS), using training-period rolling-origin validation.
4. **One-week-ahead cross-validation:** Use `stretch_tsibble(.init = 260, .step = 13)` and compare **RMSE**, **MAE**, and **MAPE**.
5. **Multi-horizon validation:** Shortlist four candidate models for each analysis and evaluate forecasts at **1, 4, 12, and 52 weeks**; use `stretch_tsibble(.init = 260, .step = 26)`, retain complete 52-week validation windows, and select using RMSE pooled across those windows. All model-selection windows use the training period, not the original holdout.
6. **Final projections:** Refit selected models on all available historical data through September 14, 2026 and produce up to 52 weeks of forecasts with uncertainty intervals.

**Interpretation note:** A one-week-ahead winner may not remain the best model across a full 52-week horizon. Cross-validation errors reported below are **not** the same as independent holdout test errors. A full-data refit is used only after the model-selection procedure.

## Branch Results: Weekly Incident Demand

The multi-horizon validation selected the following models for the three demand targets:

| Forecast target | Selected model | Pooled 52-week CV RMSE | CV MAPE | First forecast week: Sep 21, 2026 |
|---|---|---:|---:|---:|
| Total classified incidents | ARIMA(0,1,0) | 2,082.85 incidents | 7.15% | ~21,962 incidents |
| Life-threatening incidents | Automatically selected ETS | 989.70 incidents | 7.16% | ~10,630 incidents |
| Non-life-threatening incidents | ARIMA(0,1,0) | 1,404.21 incidents | 8.85% | ~11,300 incidents |

The overall demand projections are broadly stable over the forecast horizon; longer-horizon uncertainty increases. The total classified forecast is fitted separately from the two category forecasts, so the displayed estimates do not add up exactly.

**Main analysis file:** `04_transformation_cv_followup.qmd` (use its current path within the repository).

## Branch Results: EMS Response Times

Response time refers to **average time from the 911 call until an EMS unit arrives**, not just driving or travel time.

| EMS category | Selected model | Pooled 52-week CV RMSE | CV MAPE | First forecast week: Sep 21, 2026 |
|---|---|---:|---:|---:|
| Life-threatening | ARIMA(1,1,1) | 48.77 seconds | 4.97% | ~15.18 minutes |
| Non-life-threatening | ETS(A,N,N) | 190.62 seconds | 12.47% | ~40.63 minutes |

The life-threatening estimate decreases modestly over the first few weeks before stabilizing; the non-life-threatening point forecast remains approximately flat. Both models show increasing uncertainty at longer horizons. These are forecasts **relative to the September 2026 data cutoff**, not statements that those calendar dates are still in the future.

**Main analysis file:** `05_response_time_forecasting.qmd` (use its current path within the repository).

## Historical Planning Insight

The historical data show rising non-life-threatening call-to-arrival times despite incident volume not rising at the same rate. An illustrative comparison:

| Week beginning | Non-life-threatening incidents | Average call-to-arrival time |
|---|---:|---:|
| January 12, 2026 | 11,551 | 19.81 minutes |
| June 29, 2026 | 11,517 | 43.75 minutes |

These weeks had just **34 incidents** difference, yet average call-to-arrival time was approximately **24 minutes longer** in June. This illustrates why volume and arrival time should be examined separately. It **does not establish what caused the difference**. Available dispatch and travel measures can help describe where elapsed time accumulated, but confirming causes or staffing needs would require additional operational evidence.

## Reproducing the Analysis

This project uses **R**, **Quarto**, and the **fpp3 / tidyverts** forecasting ecosystem. Open the repository in RStudio or Posit, install the packages referenced by each `.qmd` file, confirm the processed-data input paths, and render the analysis documents in the order required by the repository workflow.

```bash
quarto render 04_transformation_cv_followup.qmd
quarto render 05_response_time_forecasting.qmd
```

The commands above assume both files are in the current working directory; adjust paths to match the checked-out repository. The cross-validation searches can be computationally intensive. Do not overwrite teammates' results when working on a different branch.

## What Changed on This Branch

- Expanded ARIMA and ETS configuration searches for demand and response-time forecasting.
- Added one-week-ahead rolling-origin comparisons and longer 1-, 4-, 12-, and 52-week forecast checks.
- Selected distinct models for different incident and response-time series based on longer-horizon cross-validation.
- Refit the selected models using the complete historical dataset and updated their forecasts.
- Clarified the **business objective**, forecast interpretation, and important limits on causal conclusions.

**Collaboration note:** The updated modeling work was intentionally kept separate so the team could decide whether to incorporate the results without disrupting the final report or presentation. If the updates are merged into `main`, the team README and reported model results should be reconciled with that version.

## Limitations and Next Steps

- Forecasts are estimates with uncertainty, especially further into the future.
- Operational shocks and changing EMS practices may reduce accuracy.
- The model comparison does not identify root causes of response delays or prescribe exact ambulance/staff counts.
- Category forecasts are independent of the total and are not reconciled.
- Potential future work: analyze dispatch and travel components in greater depth; incorporate staffing, unit availability, geographic, traffic, and hospital-turnaround data; periodically update and revalidate forecasts.

## Attribution

- **Data:** [NYC Open Data, 911 End-to-End Data](https://data.cityofnewyork.us/Public-Safety/911-End-to-End-Data/t7p9-n9dy).
- **Course:** ADS-506, Applied Time-Series Analysis, University of San Diego (Fall 2026).
- **Team:** Christopher Andra (data preparation/exploration and additional validation), Daniel Lopez (demand forecasting), Danitza Loya (response-time forecasting). These are project responsibilities; this branch's supplementary tuning does not imply changes have been adopted by all teammates.
