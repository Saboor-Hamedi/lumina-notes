---
id: a1b2c3d4-1120-4000-8000-000000000120
title: Time Series Analysis
language: markdown
tags: [ai-ml, machine-learning, time-series, forecasting]
selection: null
isPinned: false
timestamp: 1781500001120
---
# Time Series Analysis

Time series analysis extracts meaningful statistics and characteristics from data points collected over time. It's used for forecasting, anomaly detection, and trend analysis.

## Components

```
Time Series = Trend + Seasonality + Cycle + Residual (noise)
```

| Component | Description | Example |
|-----------|-------------|---------|
| Trend | Long-term direction | Increasing sales over years |
| Seasonality | Fixed periodic pattern | Ice cream sales peak in summer |
| Cycle | Repeating but not fixed period | Economic boom/bust cycles |
| Residual | Random noise after removing components | Unexplained variance |

## Decomposition

```python
import pandas as pd
import numpy as np
from statsmodels.tsa.seasonal import seasonal_decompose

# Additive: Y = Trend + Seasonal + Residual (constant variance)
# Multiplicative: Y = Trend × Seasonal × Residual (variance grows with level)

decomposition = seasonal_decompose(series, model='additive', period=12)
trend = decomposition.trend
seasonal = decomposition.seasonal
residual = decomposition.resid
```

## Stationarity

A time series is **stationary** if its statistical properties don't change over time:

- Constant mean
- Constant variance
- Constant autocorrelation structure

### Making a Series Stationary

```python
from statsmodels.tsa.stattools import adfuller

# Augmented Dickey-Fuller test
result = adfuller(series)
is_stationary = result[1] < 0.05  # p-value < 0.05 = stationary

# Transformations
diff_1 = series.diff().dropna()           # First differencing
diff_2 = series.diff().diff().dropna()    # Second differencing
log_series = np.log(series)               # Log transform (stabilize variance)
```

## Classical Models

| Model | Type | When to Use |
|-------|------|-------------|
| AR | Autoregressive | Correlated with past values |
| MA | Moving Average | Depends on past errors |
| ARMA | AR + MA | Stationary series |
| ARIMA | ARMA + differencing | Non-stationary, no seasonality |
| SARIMA | ARIMA + seasonality | Seasonal + non-stationary |
| VAR | Vector AR | Multiple correlated time series |

```python
from statsmodels.tsa.arima.model import ARIMA

# ARIMA(p, d, q)
# p = AR order (lags), d = differencing, q = MA order
model = ARIMA(series, order=(2, 1, 2))
result = model.fit()
forecast = result.forecast(steps=12)

# Auto-select parameters (AIC)
from pmdarima import auto_arima
auto_model = auto_arima(series, seasonal=True, m=12, trace=True)
```

## Prophet (Facebook)

```python
from prophet import Prophet

df = pd.DataFrame({'ds': dates, 'y': values})
model = Prophet(
    yearly_seasonality=True,
    weekly_seasonality=True,
    daily_seasonality=False,
    changepoint_prior_scale=0.05,  # flexibility of trend
    seasonality_prior_scale=10.0,  # flexibility of seasonality
)

# Add custom seasonality/holidays
model.add_country_holidays(country_name='US')
model.add_seasonality(name='monthly', period=30.5, fourier_order=5)

model.fit(df)
future = model.make_future_dataframe(periods=365)
forecast = model.predict(future)

model.plot(forecast)
model.plot_components(forecast)  # trend, weekly, yearly
```

## Deep Learning for Time Series

| Model | Best For |
|-------|----------|
| LSTM | Long-range dependencies, complex patterns |
| GRU | Similar to LSTM, fewer parameters |
| CNN (TCN) | Parallel training, long sequences |
| Transformer | Very long sequences, attention to patterns |
| N-BEATS | Pure MLP, interpretable, strong on M4/M5 |

```python
import torch.nn as nn

class LSTMPredictor(nn.Module):
    def __init__(self, input_dim, hidden_dim, num_layers, output_dim):
        super().__init__()
        self.lstm = nn.LSTM(input_dim, hidden_dim, num_layers, batch_first=True)
        self.linear = nn.Linear(hidden_dim, output_dim)

    def forward(self, x):
        lstm_out, _ = self.lstm(x)
        last_hidden = lstm_out[:, -1, :]
        return self.linear(last_hidden)
```

## Evaluation

| Metric | Formula | When to Use |
|--------|---------|-------------|
| MAE | mean(\|y - ŷ\|) | Interpretable (same units) |
| MSE | mean((y - ŷ)²) | Penalizes large errors |
| RMSE | sqrt(MSE) | Interpretable, penalizes large errors |
| MAPE | mean(\|(y-ŷ)/y\|) × 100 | Relative error (percentage) |
| MASE | Scaled error (independent of scale) | Comparing across series |

```python
from sklearn.metrics import mean_absolute_error, mean_squared_error

mae = mean_absolute_error(y_true, y_pred)
rmse = np.sqrt(mean_squared_error(y_true, y_pred))
mape = np.mean(np.abs((y_true - y_pred) / y_true)) * 100
```

## Best Practices

- Always visualize the data before modeling
- Check for stationarity (ADF test, KPSS test)
- Use cross-validation specific to time series (sliding window)
- Be careful with data leakage (never use future data)
- Consider external regressors (holidays, weather, promotions)
- Ensemble multiple models for better forecasts

**Links**: [[RNNs and LSTMs]] | [[Anomaly Detection]] | [[Data Engineering]] | [[Prophet]] | [[Gradient Boosting]] | [[Prometheus and Monitoring Systems]] | [[Time Series Databases]] | [[Grafana Deep Dive]]

**See also**: [[Monitoring and Observability]], [[Site Reliability Engineering]], [[Service Level Objectives]]
