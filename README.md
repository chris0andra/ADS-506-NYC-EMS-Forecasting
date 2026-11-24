# NYC Emergency Medical Services Demand and Response Time Forecasting

### ADS-506: Applied Time-Series Analysis
**University of San Diego — Fall 2026**

**Team Members:** Christopher Andra, Danitza Loya, Daniel Lopez

## Project Overview

This project analyzes New York City emergency medical services (EMS) demand and response times using weekly observations from November 2013 through September 2026. We examine historical changes, compare life-threatening and non-life-threatening emergencies, and evaluate time-series models that forecast incident volume and average call-to-arrival time.

**Business question:** Can historical EMS data help forecast future demand and response times, and how does incident demand relate to response-time performance? The goal is to provide evidence that may inform staffing, ambulance availability, and operational planning—not to claim that forecasting alone improves outcomes.

## Research Objectives

1. Identify trends, seasonal patterns, and unusual changes in weekly EMS demand.
2. Forecast weekly EMS demand and compare life-threatening and non-life-threatening categories.
3. Analyze historical response times and forecast average call-to-arrival times for both categories.
4. Compare forecasting methods using held-out observations, cross-validation, and residual diagnostics, and discuss implications for EMS resource planning.

## Data Source and Preparation

**Source:** [NYC Open Data — 911 End-to-End Data](https://data.cityofnewyork.us/Public-Safety/911-End-to-End-Data/t7p9-n9dy)

We filtered the source data to NYC EMS records and prepared a regular weekly dataset with incident counts and average response-time measures. The cleaned analysis dataset has **670 weeks**, with one row for each of two emergency categories per week (**1,340 rows**).

- **Training period:** November 18, 2013–September 15, 2025 (618 weeks).
- **Testing period:** September 22, 2025–September 14, 2026 (52 weeks).
- **Demand definition:** The cleaned data retains an overall `Total_Incidents` field that includes 23 incidents without an emergency classification. For consistent category comparisons, the total-demand forecasting models instead sum the two classified incident categories, excluding those 23 incidents. The overall total is therefore not exactly the same as modeled classified demand.

The final dataset ends on September 14, 2026; forecasts beyond this date are out-of-sample projections relative to the data cutoff.

## Methods

- Exploratory time-series analysis, trend and seasonality assessment, and STL decomposition.
- Time-ordered training/test split to avoid using future observations in model training.
- ARIMA models, including manually specified and automatically selected alternatives.
- Exponential smoothing (ETS) models and a seasonal-naïve baseline for response times.
- Forecast evaluation using **RMSE, MAE, and MAPE** on a 52-week holdout period.
- Rolling-origin cross-validation, alternative training-window comparisons, and residual diagnostics (including Ljung–Box tests).
- Forecasts for the next 52 weeks with 80% and 95% prediction intervals.

## Selected Findings

**Demand forecasting (52-week test period):**

| Forecast target | Best model in main comparison | RMSE | MAPE |
| --- | --- | ---: | ---: |
| Classified total demand | ARIMA | ~1,250 incidents | 5.22% |
| Life-threatening demand | ETS | ~761 incidents | 6.51% |
| Non-life-threatening demand | ETS | ~715 incidents | 5.61% |

For **one-week-ahead rolling-origin cross-validation**, ETS had the lowest errors among the ARIMA/ETS comparisons for all three demand targets. This illustrates that the preferred model can depend on the forecast horizon.

**Response-time forecasting (52-week test period):** ETS produced the lowest RMSE and MAE for both emergency categories; manually specified ARIMA(0,1,1) produced slightly lower MAPE. Refit ETS models projected average weekly call-to-arrival times of approximately **15.53 minutes** for life-threatening emergencies and **43.48 minutes** for non-life-threatening emergencies, using the data available through September 14, 2026.

**Demand and response times:** The association between incident volume and response time varied by emergency category and historical period. Correlation does not establish that higher demand caused slower responses.

**Important limitations:** Some ETS residuals retained statistically significant autocorrelation; forecast uncertainty increased over longer horizons; and some response-time prediction intervals extended below zero, which is not physically meaningful. These forecasts are planning aids, not guarantees of future outcomes.

## Repository Organization

```text
code/                 Quarto (.qmd) analysis files 01–05
  01_*               Data cleaning and exploratory analysis
  02_*               Chronological training/test split
  03_*               EMS demand forecasting
  04_*               Transformations, validation, and future demand forecasts
  05_*               Response-time analysis and forecasting
data/
  raw/                Original data source
  processed/          Cleaned weekly data and train/test CSV files
README.md             Project overview and instructions
```

## Reproducing the Analysis

1. Open the project in **RStudio** or **Posit Cloud**, with the repository folder structure intact.
2. Install the R packages used by the Quarto files, including `tidyverse`, `fpp3`, `here`, and `gt`.
3. Place `911_End-to-End_Data_20261104.csv` in `data/raw/` and make sure the `data/processed/` folder exists.
4. Run the numbered `.qmd` files in order, starting with data preparation, then the split and forecasting analyses. Render the files with Quarto to inspect figures, tables, and narrative results.

**Path note:** The Quarto files use `here::here()` to locate data from the repository root. Open the repository as a project in Posit Cloud and render Files 01–05 in order. File 01 generates `ems_weekly_clean.csv`, and File 02 generates `ems_train.csv` and `ems_test.csv` for Files 03–05.

## Team Contributions

- **Christopher Andra:** Project development, data cleaning, exploratory time-series analysis, training/testing preparation, additional model development and validation.
- **Daniel Lopez:** EMS demand forecasting, ARIMA model selection, ETS comparisons, and forecasting evaluation.
- **Danitza Loya:** Additional forecasting analysis, transformations, cross-validation, response-time modeling, and evaluation.

All team members contributed to the project through collaborative analysis and shared deliverables.

## AI Assistance and Academic Integrity

AI-assisted tools were used to support aspects of code development, troubleshooting, explanation, editing, readability, and improving visualizations. The team remains responsible for verifying the methods, results, and interpretations.

## Project Status

**Coding and forecasting analysis completed.** The five Quarto analysis files have been rendered and reviewed. The technical findings are intended to support the final written report and narrated project presentation.
