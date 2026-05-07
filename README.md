# Applied Economics – R Analysis Portfolio

Three R analyses completed as part of an applied economics and data science assessment. Each folder is self-contained with its own `.Rmd` file. The analyses cover time series forecasting of a macroeconomic indicator and natural language processing of a large central bank speech corpus.

---

## Repository Structure

```
Q1-Mexico-Currency-Forecast/
    Mexico_Currency_Forecast.Rmd

Q3-BIS-Speeches-Analysis/
    BIS_Speeches_Exploration.Rmd

Q4-BIS-Hawk-Dove-Scoring/
    BIS_Hawk_Dove_Scoring.Rmd
```

> **Note on ordering:** The scripts are numbered Q1, Q3, and Q4 to reflect the structure of the original assessment. Q3 must be run before Q4, as Q4 depends on the `speeches` object and country assignments produced in Q3.

---

## Q1 – Mexico Currency in Circulation: Liquidity Forecasting

**Data source:** Banco de México (Banxico) SIE – daily currency in circulation series (MXN, nominal levels), January 2011 onward.

**What this does:**

- Visualizes the full level series and performs STL decomposition to isolate trend, seasonal, and remainder components
- Documents a clear annual seasonality pattern: December peak driven by holiday spending, September–October trough
- Converts to a weekly-frequency `ts` object and applies Box-Cox variance stabilization
- Runs a 30-day out-of-sample holdout comparison between SARIMA (`auto.arima`) and ETS(A,A,A), evaluated on RMSE, MAE, and MAPE
- Selects the better-performing model (ETS), refits on the full series, and produces a 20 working-day point forecast with 80% and 95% prediction intervals
- Weekday filtering uses a calendar-day overgeneration approach to correctly handle weekend boundaries without hard-coded date arithmetic

**Key result:** ETS(A,A,A) outperformed SARIMA on the holdout across all three error metrics, consistent with the series' smooth seasonal structure and steady trend.

---

## Q3 – BIS Speeches: Corpus Exploration and Inflation Persistence

**Data source:** BIS central bank speech corpus (`BIS_speeches.csv`).

**What this does:**

- Loads and cleans a large text dataset (deduplication, date parsing, whitespace normalization)
- Computes corpus-level descriptive statistics: total speeches, date range, unique speakers, word count distribution, share mentioning "inflation"
- Assigns each speech to a country using a keyword lookup table on the institution description and author fields
- Builds a binary flag for inflation persistence language using a curated 10-pattern regex (e.g., "inflation remains elevated," "second-round effects") designed to minimize false positives
- Plots the share of speeches flagged for persistence language by year

---

## Q4 – BIS Speeches: Hawk/Dove Sentiment Scoring

**Data source:** `speeches` object from Q3 (run Q3 first).

**What this does:**

- Constructs a lexicon-based hawk/dove scoring system: hawk_score = hawk_hits / (hawk_hits + dove_hits), with 0.5 assigned to speeches with no keyword matches
- Scores the top-10 most frequent speakers and the full corpus
- Identifies the most hawkish and most dovish speeches with supporting text snippets for manual validation
- Aggregates scores to speaker-year level and runs OLS regressions of annual average hawk_score on year (one regression per speaker) to detect tone trends over time
- Aggregates to country-year level to rank countries by average hawkishness and by stability (standard deviation) of policy communication tone
- Produces time-series plots for selected speakers and countries (US, Euro area, Japan)

---

## Dependencies

All analyses use R. Required packages:

```r
install.packages(c("dplyr", "lubridate", "ggplot2", "forecast",
                   "readr", "stringr", "purrr", "scales"))
```

---

## Notes

- Data files (`Bank Data.csv`, `BIS_speeches.csv`) are not included in this repository as they are proprietary to the original assessment.
- All output figures are saved to `figures/` and tables to `tables/` within each script's working directory.
