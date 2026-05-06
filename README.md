# Bishkek Air Quality Forecasting — PM2.5 Time Series Analysis (2019–2024)

> Time series forecasting of PM2.5 air pollution in Bishkek, Kyrgyzstan using 5 years of hourly sensor data (44,155 observations). Built an Autoregressive AR(26) model with walk-forward validation, reducing forecasting error by **30.7% vs. baseline** and identifying dangerous seasonal pollution patterns.

![Python](https://img.shields.io/badge/Python-3.10+-blue?logo=python&logoColor=white)
![statsmodels](https://img.shields.io/badge/statsmodels-AR%20%7C%20ARIMA-green)
![Data](https://img.shields.io/badge/Data-44%2C155%20hourly%20readings-purple)
![Coverage](https://img.shields.io/badge/Coverage-2019–2024-orange)
![Domain](https://img.shields.io/badge/Domain-Public%20Health%20%7C%20Environment-red)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen)

---

## Why This Project Matters

Bishkek consistently ranks among the most polluted cities in Central Asia. PM2.5 — fine particulate matter smaller than 2.5 micrometers — penetrates deep into lung tissue and is linked to cardiovascular and respiratory disease. Reliable short-term forecasting of PM2.5 levels allows residents, hospitals, and city planners to take preventative action.

This project builds and validates a forecasting pipeline on 5 years of real hourly sensor data from the U.S. Embassy monitoring station in Bishkek.

---

## Results

### Model Comparison

| Model | MAE | vs. Baseline |
|-------|:---:|:------------:|
| Baseline (training mean = 31.47 µg/m³) | 27.02 | — |
| AR(26) — standard test split | 30.04 | Worse (extrapolation degrades) |
| **AR(26) — walk-forward validation** | **18.72** | **30.7% ↓** |

**Key takeaway:** Standard train/test splitting fails for time series — the model's predictions degrade as it gets further from its training window. Walk-forward validation (retraining at each step) reduces test MAE from 30.04 → **18.72**, a 37.7% improvement over the naive split and 30.7% better than the baseline.

### Training Performance

| Metric | Value |
|--------|-------|
| Training MAE — AR(26) | **9.55 µg/m³** |
| Test MAE — standard split | 30.04 µg/m³ |
| Test MAE — walk-forward validation | **18.72 µg/m³** |
| Baseline MAE | 27.02 µg/m³ |

---

## Dataset

- **Source:** U.S. Embassy Bishkek PM2.5 monitoring station (via EPA AirNow)
- **Coverage:** February 2019 – February 2024
- **Granularity:** 1-hour intervals
- **Total observations (after cleaning):** 44,155
- **Key features:** Raw PM2.5 concentration (µg/m³), NowCast Concentration, AQI, AQI Category
- **Preprocessing:**
  - Removed invalid readings (values ≤ 0 and > 500 µg/m³ per EPA guidelines)
  - Resampled to strict 1-hour frequency
  - Forward-filled missing hours

### Peak Reading

| Metric | Value | When |
|--------|-------|------|
| Highest PM2.5 recorded | **494 µg/m³** (Hazardous) | 2021-01-03 22:00 |
| Mean PM2.5 (training set) | 31.47 µg/m³ (Moderate) | — |

---

## Key Findings

**1. Bishkek air quality is strongly seasonal — winters are hazardous.**
Box plots by month show dramatically elevated PM2.5 in November–February (median often > 80 µg/m³, WHO limit = 15 µg/m³/day). This is driven by coal heating, temperature inversions trapping pollution in the Chuy Valley, and increased vehicle idling.

**2. Strong 24-hour autocorrelation confirms daily pollution cycles.**
The ACF plot shows significant autocorrelation at lags 1, 24, 48 — confirming daily rhythms (morning/evening traffic peaks) and weekly patterns. This guided the choice of lag=26 for the AR model.

**3. The AR(26) model's primary driver is the immediately preceding hour (L1 coefficient = 0.711).**
Beyond L1, contributions drop sharply. Lags at 15, 22, and 24 hours still show meaningful coefficients (0.044, 0.046, 0.038), consistent with the daily cycle.

**4. Walk-forward validation is the correct evaluation method for this problem.**
Standard test-set evaluation (MAE = 30.04) made the model look worse than baseline. Walk-forward validation (MAE = 18.72) correctly captures real-world forecasting performance by simulating how the model would be used operationally — retraining on all available history before each prediction.

**5. Model degrades beyond ~24-hour horizons.**
The AR model is a strong 1-step-ahead forecaster but loses accuracy at longer horizons. SARIMA or ML-based approaches (XGBoost with lag features) would be needed for multi-day forecasting.

---

## Approach

### Data Pipeline
1. Load 7 separate yearly CSV files (2019–2024)
2. Concatenate into a single 42,855-row DataFrame
3. Extract `Raw Conc.` (PM2.5 µg/m³) with timestamps
4. Remove outliers (< 0 and > 500 µg/m³)
5. Resample to strict 1-hour frequency, forward-fill gaps
6. Result: 44,155-observation hourly time series

### Exploratory Analysis
- Violin plots of PM2.5 by AQI category
- Stacked bar plots of AQI distribution by month
- Box plots by month and season (Winter/Spring/Summer/Autumn)
- 7-day rolling average to identify long-term trends
- Lag plots and autocorrelation plots to assess time dependence
- ACF and PACF plots to determine optimal AR lag order

### Modeling
- **Baseline:** Predict training set mean for all test observations (MAE = 27.02)
- **AR(26):** AutoReg model with 26 lags, trained on 95% of data
  - Standard evaluation: MAE = 30.04 (misleading — see below)
  - Walk-forward validation: MAE = 18.72 ✓
- **Walk-Forward Validation:** At each test step, retrain AR(26) on all history up to that point, predict one step ahead, then advance

### Train/Test Split
- **Train:** 95% (first 41,947 hourly observations)
- **Test:** 5% (last 2,208 hourly observations: Nov 2023 – Feb 2024)

---

## EPA PM2.5 Reference Table

| PM2.5 (µg/m³) | AQI Category | Health Impact |
|:--------------:|-------------|---------------|
| 0 – 12.0 | Good | Little to no risk |
| 12.1 – 35.4 | Moderate | Unusually sensitive individuals affected |
| 35.5 – 55.4 | Unhealthy for Sensitive Groups | Respiratory symptoms in sensitive groups |
| 55.5 – 150.4 | Unhealthy | Increased risk for general population |
| 150.5 – 250.4 | Very Unhealthy | Significant health risk for all |
| 250.5 – 500.4 | Hazardous | Serious risk — avoid all outdoor activity |

---

## Project Structure

```
air-quality-bishkek-2019-2024/
│
├── ML_Final_Project_.ipynb         # Full pipeline: EDA → modeling → walk-forward validation
├── air_quality_bufatima.pdf        # Written project report
├── requirements.txt                # Dependencies
└── README.md
```

> **Note:** The 7 CSV data files (2019–2024) are required to run the notebook. These can be downloaded from the [EPA AirNow](https://www.airnow.gov/international/us-embassies-and-consulates/) international monitoring portal for Bishkek.

---

## How to Run

```bash
git clone https://github.com/Bufatima-Nk/air-quality-bishkek-2019-2024
cd air-quality-bishkek-2019-2024
pip install -r requirements.txt
jupyter notebook ML_Final_Project_.ipynb
```

---

## Tech Stack

| Category | Tools |
|----------|-------|
| Time Series Modeling | statsmodels (AutoReg, ARIMA, seasonal_decompose) |
| Data | pandas (resample, rolling, DatetimeIndex), NumPy |
| Visualization | Matplotlib, Seaborn, Plotly Express |
| Evaluation | scikit-learn (mean_absolute_error) |

---

## Limitations & Future Work

- **AR model horizon:** Reliable for 1-step-ahead (1 hour) forecasting. Multi-day forecasting requires SARIMA or ML approaches.
- **SARIMA:** Adding seasonal differencing (period=24 for daily, 168 for weekly) would explicitly model the strong seasonal patterns observed in ACF.
- **External features:** Weather data (temperature, wind speed, humidity) and coal heating consumption data would likely significantly improve predictions — PM2.5 in Bishkek is heavily driven by meteorology.
- **Real-time deployment:** A Streamlit app that pulls live EPA data and generates next-24-hour PM2.5 forecasts would make this immediately useful for Bishkek residents.
- **Anomaly detection:** Flag readings that exceed WHO thresholds as a public health alerting system.

---

## Author

**Bufatima N.K.**

[![LinkedIn](https://img.shields.io/badge/LinkedIn-bufatima--n--k-blue?logo=linkedin)](https://linkedin.com/in/bufatima-n-k)
[![GitHub](https://img.shields.io/badge/GitHub-Bufatima--Nk-black?logo=github)](https://github.com/Bufatima-Nk)
