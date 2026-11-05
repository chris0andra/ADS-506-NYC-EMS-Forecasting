# NYC Emergency Medical Services Demand and Response Time Forecasting

### ADS-506: Applied Time-Series Analysis
**University of San Diego — Fall 2026**

**Team Members:** Christopher Andra, Danitza Loya, Daniel Lopez

## Project Overview

This project examines emergency medical services (EMS) demand and response times in New York City using historical weekly data from 2013 to 2026.

The goal is to apply time-series analysis and forecasting methods to understand emergency demand, compare life-threatening and non-life-threatening emergencies, and investigate response-time patterns.

Our findings may help emergency services anticipate demand and support resource planning.

## Research Objectives

1. Analyze historical EMS demand, trends, and seasonality.
2. Develop and compare forecasting models for weekly EMS incidents.
3. Compare demand patterns between life-threatening and non-life-threatening emergencies.
4. Analyze and forecast EMS response times.

## Dataset

**Source:** NYC Open Data — 911 End-to-End Data

https://data.cityofnewyork.us/Public-Safety/911-End-to-End-Data/t7p9-n9dy

The dataset contains weekly emergency incident counts and response-time measurements. Our analysis focuses on EMS records.

## Initial Team Responsibilities

### Member 1 — Data Cleaning & Time-Series EDA
- Import, clean, and validate the dataset.
- Check missing values, outliers, and data quality.
- Explore trends, seasonality, and unusual events.
- Create time-series visualizations and STL decomposition.
- Save the cleaned dataset in `data/processed/`.

### Member 2 — EMS Demand Forecasting
- Forecast total weekly EMS incidents.
- Compare life-threatening and non-life-threatening emergency demand.
- Build and compare appropriate forecasting models.
- Evaluate forecast accuracy and visualize results.

### Member 3 — EMS Response-Time Analysis & Forecasting
- Explore response-time trends for both emergency categories.
- Examine seasonality and unusual changes in response times.
- Build and evaluate response-time forecasting models.
- Investigate the relationship between incident counts and response times.


*Responsibilities are preliminary and may be adjusted as the project develops.*

## Tools

- R and RStudio / Posit Cloud
- Quarto
- GitHub
- `fpp3` and related R forecasting packages

## Repository Organization

- `data/raw/` — Original, unchanged dataset
- `data/processed/` — Cleaned data for analysis
- `code/` — R/Quarto code for each team member

## Project Status

The team has selected NYC EMS as its final project topic. Preliminary data checks and forecasting have been completed to assess feasibility. Final analysis, modeling, and evaluation will be conducted collaboratively.
