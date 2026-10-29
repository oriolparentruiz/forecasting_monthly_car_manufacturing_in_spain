# 🚗 Forecasting Monthly Car Manufacturing in Spain

Time series analysis and forecasting of monthly passenger car production in Spain using the **Box-Jenkins (ARIMA/SARIMA) methodology** in R.

> Project for the *Time Series Analysis* course (AD) · Bachelor's in Data Science and Engineering (GCED) · Academic year 2024–2025
> Authors: **Lucas Viner** and **Oriol Parent**

---

## 📌 Overview

The goal is to build a robust, interpretable model able to forecast the monthly production of passenger cars in Spain (in thousands of units), going beyond a simple model fit by including residual diagnostics, out-of-sample validation, calendar-effect correction and automatic outlier treatment.

**Data:** `TURISMOS` series, monthly passenger cars manufactured in Spain from January 1994 onward. Source: *Estadística de Fabricación de Vehículos*, Ministerio de Industria, Energía y Turismo.

## 🔬 Methodology

1. **Identification**
   - Log transformation to stabilise variance (year-wise boxplots showed heteroscedasticity).
   - One regular and one seasonal difference (`d = 1`, `D = 1`, `s = 12`), chosen by minimum variance.
   - ACF/PACF analysis to propose four candidate models.
2. **Estimation**
   - Maximum likelihood estimation with `arima()`; intercept tested and discarded.
   - Two parsimonious models compared:
     - `ARIMA(0,1,1)(1,1,0)₁₂`
     - `ARIMA(0,1,1)(0,1,1)₁₂`
3. **Validation**
   - Residual analysis: homoscedasticity, normality (Q-Q plot, histogram, Shapiro-Wilk) and independence (ACF/PACF, Ljung-Box).
   - Invertibility check (MA roots), AIC/BIC, parameter stability (full vs. truncated sample).
   - Out-of-sample accuracy using the last 12 observations (MAE, RMSE, MAPE).
4. **Forecasting**
   - 12-month-ahead forecasts with confidence intervals.
5. **Calendar effects**
   - Regressors for trading days and Easter, which turned out to be statistically significant.
6. **Outlier treatment**
   - Automatic detection of additive outliers (AO), level shifts (LS) and temporary changes (TC), and refitting on the cleaned series.

## 📊 Key results

| Model | AIC | BIC | RMSE | MAE | MAPE |
|---|---|---|---|---|---|
| ARIMA(0,1,1)(1,1,0)₁₂ | -283.86 | -272.89 | 22.54 | 17.96 | 0.103 |
| **ARIMA(0,1,1)(0,1,1)₁₂** (selected) | **-326.25** | **-315.27** | 25.28 | 20.77 | 0.125 |

- The **airline-type model** `ARIMA(0,1,1)(0,1,1)₁₂` was selected for its clearly better AIC/BIC, even though the first model had slightly lower forecast error on the 12-month hold-out.
- Both models are stable and invertible. Residuals are not normal and show mild heteroscedasticity.
- Correcting for **calendar effects** improved AIC/BIC and residual independence (Ljung-Box).
- Removing **outliers** (e.g. the 2008–2009 financial crisis appears as transitory changes) gave the largest improvement in fit and forecast accuracy.

| Metric | Original model | Adjusted (calendar + outliers) |
|---|---|---|
| MAE | 17.96 | 16.89 |
| RMSE | 22.54 | 21.57 |
| AIC | -326.25 | -621.28 |

## 📁 Repository structure

```
.
├── car_manufacturing_forecast.Rmd   # Full analysis and report source (R Markdown)
├── TimeSeries.pdf                   # Rendered final report
├── turismos.dat                     # Dataset (monthly cars, read in the Rmd)
├── CalendarEffects.R                # Helper: trading-day and Easter regressors
└── README.md
```

> ⚠️ `turismos.dat` and `CalendarEffects.R` are loaded by the `.Rmd`. Make sure they are in the same folder (they were provided by the course) before knitting.

## ▶️ How to reproduce

**Requirements:** R (≥ 4.0), RStudio (recommended) and a LaTeX distribution with `xelatex` (e.g. TinyTeX).

```r
install.packages(c("rmarkdown", "knitr"))
```

1. Clone the repository:
   ```bash
   git clone https://github.com/<your-username>/<repo-name>.git
   ```
2. Open `car_manufacturing_forecast.Rmd` in RStudio.
3. Knit to PDF. The `report` parameter in the YAML header controls the output:
   - `report: true` → clean report (code, messages and figures hidden).
   - `report: false` → shows code, messages and plots, useful for exploring the analysis.

## 🛠️ Tools

- **R** · `arima`, `forecast`-style diagnostics, `scatter.smooth`, Ljung-Box and Shapiro-Wilk tests
- **R Markdown / knitr** · reproducible report with `xelatex`

## 📚 References

The report is based on the procedures taught in the Time Series Analysis course; no additional external sources were used.

## 👥 Authors

- **Lucas Viner**
- **Oriol Parent**
