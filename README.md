# Bakken Production Trend Analysis & Forecast

Aggregate production-history review and decline-curve forecast for 130 Bakken wells, covering May 2003 – December 2025 with a forecast horizon through December 2027.

## Contents

| File | Description |
|---|---|
| `Bakken_Production_Analysis.ipynb` | Full analysis notebook — data cleaning, trend analysis, decline-curve forecasting, and all figures/tables |
| `DCA_DATA.xlsx` | Source production dataset (monthly well-level oil, gas, water, producing days) |
| `Bakken_Production_Trend_and_Forecast_Editable_Report.docx` | Formatted write-up of the analysis (report deliverable) |

## Data

Each row is one well-month production record:

| Column | Description |
|---|---|
| `API #` | Unique well identifier |
| `RptDate` | End-of-month reporting date |
| `Formation` | Producing formation (Bakken) |
| `Lease_Unit` | Lease/unit identifier |
| `BBLS_OIL_COND` | Monthly oil/condensate volume (bbl) |
| `MCF_GAS` | Monthly gas volume (MCF) |
| `BBLS_WTR` | Monthly produced-water volume (bbl) |
| `DAYS_PROD` | Producing days reported for the well that month |

**Raw records:** 29,692 → **250 exact duplicates removed** → **29,442 clean well-month records** across 130 wells.

## Methodology

1. **Cleaning** — remove exact duplicate rows; flag but retain negative-volume corrections and `DAYS_PROD` anomalies.
2. **Aggregation** — monthly and annual oil/gas/water totals, calendar-day field rate (BOPD), active-well counts (rows with `DAYS_PROD > 0`), GOR and water cut (monthly ratios averaged per year).
3. **Well ranking** — cumulative and 2025 oil production by well.
4. **Decline-curve analysis (DCA)** — exponential, harmonic, and hyperbolic models fit to field calendar-day oil rate over multiple trailing history windows (60–180+ months).
5. **Model selection** — each model/window combination is fit through December 2024 and backtested against actual 2025 monthly production; the configuration with the lowest MAPE is selected.
6. **Forecast** — the selected model (hyperbolic, 120-month window) is refit through December 2025 and projected to December 2027, with an approximate 95% range from a parametric Monte Carlo simulation (parameter covariance + resampled backtest residuals).

## Key results

| Metric | Value |
|---|---|
| Peak monthly oil | 333,099 bbl (Dec 2006) |
| Peak annual oil | 3,528,543 bbl (2007) |
| 2025 oil production | 535,289 bbl (−84.8% vs. peak) |
| Selected model | Hyperbolic, 120-month window |
| Backtest (2025) | MAPE 5.8% · MAE 2,674 bbl/mo · RMSE 3,258 bbl/mo |
| Fitted parameters | qi = 2,784 BOPD · Di = 0.0126/month · b = 2.00 |
| 2026 P50 forecast | 497,842 bbl (−7.0% vs. 2025) |
| 2027 P50 forecast | 480,676 bbl (−3.4% vs. 2026) |

## Setup

```bash
pip install pandas numpy scipy matplotlib openpyxl jupyter
```

## Usage

```bash
jupyter notebook Bakken_Production_Analysis.ipynb
```

Run all cells top to bottom. The notebook expects `DCA_DATA.xlsx` in the same directory as `DATA_PATH` at the top of the notebook — update that path if the file lives elsewhere.

## Limitations

- Aggregate forecast only; individual wells are not modeled separately.
- No future drilling, workovers, shut-ins, or facility constraints are incorporated.
- The fitted hyperbolic *b* reached the imposed upper bound (2.0), which can produce optimistic long-term tails — forecasts beyond 2027 should apply terminal decline and economic-limit assumptions.
- The statistical interval reflects curve-fit and backtest-residual uncertainty only; it is not a petroleum-reserves P10/P50/P90 classification and does not capture commodity-price or operational risk.

## License

Internal analysis project — add a license here if this repository will be made public.
