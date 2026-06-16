# Financial Distress & ESG Performance Analysis

> Applied Data Science project — examining the relationship between corporate financial health (Altman Z-score) and ESG risk exposure using RepRisk incident data for US firms (2018–2021).



---

## Overview

This project investigates whether financially distressed firms exhibit higher ESG risk scores, measured through reported ESG incidents. We build a panel dataset of US publicly listed companies by merging:

- **RepRisk** incident-level ESG data
- **RepRisk** universe (firm-year coverage)
- **Compustat** financial data

We compute Altman Z-scores as a proxy for financial distress, construct weighted ESG risk scores (severity × reach), and test the relationship using OLS regressions, extended models with controls, year-by-year analysis, and two-way panel fixed effects.

---

## Data Sources

| File | Description |
|---|---|
| `data/financial_data.csv` | Compustat financials (assets, liabilities, EBIT, market cap, sales, etc.) |
| `data/reprisk_risk_incidents.csv` | RepRisk ESG incident records with severity, reach, and E/S/G flags |
| `data/reprisk_universe.csv` | RepRisk universe of covered firms by year |

> **Note:** The raw data files are not included in this repository. You will need access to RepRisk and Compustat to reproduce the analysis.

---

## Methodology

### ESG Score Construction

Each incident is weighted by `severity × reach`. Scores are aggregated at the firm-year level for:
- **E** — Environmental incidents
- **S** — Social incidents
- **G** — Governance incidents
- **ESG** — Combined total

A count-based score is also retained for robustness.

### Financial Distress Measure

We compute the **Altman Z-score** using the standard formula:

~~~
Z = 1.2×(WC/TA) + 1.4×(RE/TA) + 3.3×(EBIT/TA) + 0.6×(MktCap/TL) + 1.0×(Sales/TA)
~~~

Firms are classified into three zones:

| Zone | Z-score |
|---|---|
| Distress | Z < 1.81 |
| Grey | 1.81 ≤ Z ≤ 2.99 |
| Safe | Z > 2.99 |

Financial (SIC 6000–6999) and utility (SIC 4900–4999) firms are excluded. Financials are lagged by one year relative to ESG data.

### Regression Models

1. **Baseline OLS** — ESG score ~ Z-score (HC3 robust SE)
2. **Extended OLS** — + firm controls (log assets, leverage, ROA) + year & industry FE
3. **Year-by-year** — separate regressions per year
4. **Panel models** — Pooled OLS vs. two-way fixed effects (firm + year), clustered SE

---

## Key Findings

- **Baseline:** higher Z-score is negatively associated with ESG incidents (p < 0.001).
- **Sign flip after controls:** once firm size is added, the coefficient reverses — a classic Simpson's Paradox driven by the fact that large firms have both higher Z-scores and more reported incidents (media visibility).
- **Fixed effects:** the within-firm coefficient is near zero and insignificant, suggesting the pattern is **cross-sectional** rather than driven by changes within firms over time.

---

## Project Structure

~~~
├── ADS_Project_Analysis_Final.ipynb   # Main analysis notebook
├── data/
│   ├── financial_data.csv             # Compustat financials (not included)
│   ├── reprisk_risk_incidents.csv     # RepRisk incidents (not included)
│   └── reprisk_universe.csv           # RepRisk universe (not included)
└── final_dataset.csv                  # Exported merged dataset (generated)
~~~

---

## Requirements

bash
pip install pandas numpy matplotlib seaborn scipy statsmodels linearmodels


| Package | Purpose |
|---|---|
| `pandas`, `numpy` | Data manipulation |
| `matplotlib`, `seaborn` | Visualisation |
| `scipy` | Statistical tests (Spearman correlation) |
| `statsmodels` | OLS regression |
| `linearmodels` | Panel data models (Pooled OLS, Fixed Effects) |

---

## Reproducing the Analysis

1. Clone this repository
2. Place the three raw data files in a `data/` folder
3. Open and run `ADS_Project_Analysis_Final.ipynb` top to bottom

The notebook will produce all charts, regression tables, and export `final_dataset.csv`.