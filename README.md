# NIFTY 50 Time Series Analysis and Forecasting

## Problem Statement

The objective of this project is to analyze historical NIFTY 50 stock market data, identify trends, seasonality, volatility patterns, and forecast future index values using time series modeling techniques.

Financial market forecasting helps investors, analysts, and institutions understand market behavior and make data-driven investment decisions.

---

## Dataset Details

**Dataset:** Stock Market Sensex & NIFTY All-Time Dataset

**Source:** Kaggle

**Link:** https://www.kaggle.com/datasets/rockyt07/stock-market-sensex-nifty-all-time-dataset

### Dataset Overview

* Total Records Before Cleaning: **4452**
* Total Records After Cleaning: **4403**
* Time Period: **September 2007 onwards**
* Market Index: **NIFTY 50**

### Features

| Feature        | Description                           |
| -------------- | ------------------------------------- |
| Date           | Trading date                          |
| Adj Close      | Adjusted closing price                |
| Close          | Closing price                         |
| High           | Highest price of the day              |
| Low            | Lowest price of the day               |
| Open           | Opening price                         |
| Volume         | Trading volume                        |
| SMA_20         | 20-day Simple Moving Average          |
| SMA_50         | 50-day Simple Moving Average          |
| EMA_12         | 12-day Exponential Moving Average     |
| EMA_26         | 26-day Exponential Moving Average     |
| MACD           | Moving Average Convergence Divergence |
| Signal_Line    | MACD Signal Line                      |
| RSI_14         | Relative Strength Index (14-day)      |
| BB_Mid         | Bollinger Band Middle Line            |
| BB_Upper       | Bollinger Band Upper Band             |
| BB_Lower       | Bollinger Band Lower Band             |
| Daily_Return_% | Daily percentage return               |

---

## Approach

### 1. Data Loading and Cleaning

* Loaded the NIFTY 50 dataset from Kaggle.
* Examined data types, null values, and dataset structure.
* Removed rows containing missing values.
* Converted the `Date` column into datetime format.
* Set `Date` as the DataFrame index for time-series analysis.

### 2. Exploratory Data Analysis (EDA)

Several visualizations were created to understand market behavior:

#### Trend Analysis

* Closing Price Trend
* Closing Price with:

  * Bollinger Bands
  * 50-Day Moving Average

#### Technical Indicators

* MACD Analysis
* RSI (Relative Strength Index)
* Moving Average Comparison:

  * SMA 20
  * SMA 50
  * EMA 12
  * EMA 26

#### Distribution Analysis

* Trading Volume Distribution
* Daily Returns Distribution

#### Correlation Analysis

A correlation heatmap was generated to study relationships between technical indicators and market prices.

---

## Seasonality Analysis

To identify recurring patterns in the market:

### Seasonal Decomposition

The closing prices were transformed using logarithms and decomposed into:

* Trend Component
* Seasonal Component
* Residual Component

using:

```python
seasonal_decompose(
    series,
    model='additive',
    period=252
)
```

where 252 represents approximately one trading year.

### STL Decomposition

To better handle changing seasonal amplitudes and market shocks, STL decomposition was applied:

```python
STL(
    series,
    period=252,
    robust=True
)
```

This method produced a more reliable separation of trend, seasonality, and residual noise.

---

## Volatility Analysis

Market volatility was measured using a rolling standard deviation of daily returns.

```python
rolling_std = Daily_Return_%.rolling(20).std()
```

Observations:

* Higher rolling standard deviation indicates increased uncertainty.
* Volatility spikes correspond to periods of significant market movements.

---

## Stationarity Testing

Time-series forecasting models require stationary data.

### Augmented Dickey-Fuller (ADF) Test

#### Original Closing Prices

| Metric        | Value          |
| ------------- | -------------- |
| ADF Statistic | 1.2678         |
| p-value       | 0.9964         |
| Conclusion    | Non-Stationary |

Since the p-value was much greater than 0.05, the series was found to be non-stationary.

### First Differencing

The closing price series was differenced:

```python
Close_diff = Close.diff()
```

#### Differenced Series

| Metric        | Value      |
| ------------- | ---------- |
| ADF Statistic | -15.3616   |
| p-value       | 0.0000     |
| Conclusion    | Stationary |

The differenced series became stationary and suitable for ARIMA modeling.

---

## ARIMA Model Development

### Model Identification

ACF and PACF plots were analyzed to determine suitable ARIMA parameters.

Observations showed:

* No significant autocorrelation after first differencing.
* Differenced series behaved like white noise.

Therefore:

```python
ARIMA(0,1,0)
```

was selected.

### Model Interpretation

ARIMA(0,1,0) represents a Random Walk model where:

* p = 0 (No autoregressive terms)
* d = 1 (First differencing)
* q = 0 (No moving-average terms)

---

## Forecasting

The ARIMA model was trained on historical NIFTY closing prices and used to forecast the next:

* 30 trading days

Forecast results were visualized along with:

* Historical prices
* Predicted values
* 95% Confidence Intervals

---

## Model Evaluation

A holdout evaluation strategy was used.

### Train-Test Split

* Training Data: All observations except the last 30 trading days
* Testing Data: Last 30 trading days

### Evaluation Metrics

#### Mean Absolute Error (MAE)

Measures average prediction error.

#### Root Mean Squared Error (RMSE)

Measures prediction accuracy while penalizing larger errors.

#### Mean Absolute Percentage Error (MAPE)

Measures average percentage prediction error.

---

## Results

| Metric | Value  |
| ------ | ------ |
| MAE    | 594.54 |
| RMSE   | 686.42 |
| MAPE   | 2.32%  |

### Interpretation

* The model achieved a low forecasting error relative to the index value.
* A MAPE of 2.32% indicates that the average prediction error was approximately 2.32%.
* The ARIMA model successfully captured the overall trend despite the inherent randomness of stock market movements.

---

## Key Findings

1. NIFTY 50 exhibits a strong long-term upward trend.
2. The original closing price series is non-stationary.
3. First differencing successfully transforms the series into a stationary process.
4. After differencing, little autocorrelation remains, suggesting near-random-walk behavior.
5. Market volatility varies over time, with identifiable periods of heightened uncertainty.
6. ARIMA(0,1,0) provides a reasonable baseline forecasting model for short-term prediction.

---

## Conclusion

This project performed a comprehensive time-series analysis of NIFTY 50 historical market data. The analysis included trend visualization, technical indicator evaluation, seasonality detection, volatility assessment, stationarity testing, and forecasting.

The Augmented Dickey-Fuller test confirmed that the original price series was non-stationary, while first differencing produced a stationary series suitable for forecasting. Based on ACF and PACF analysis, an ARIMA(0,1,0) model was selected and evaluated.

The final model achieved:

* MAE: 594.54
* RMSE: 686.42
* MAPE: 2.32%

These results demonstrate that a simple ARIMA model can provide useful short-term forecasts for NIFTY 50 index movements while serving as a strong baseline for future improvements.

### Future Work

Potential enhancements include:

* SARIMA for seasonal effects
* ARIMA hyperparameter optimization
* GARCH models for volatility forecasting
* Facebook Prophet forecasting
* LSTM and GRU deep learning models
* Multivariate forecasting using technical indicators and macroeconomic variables
