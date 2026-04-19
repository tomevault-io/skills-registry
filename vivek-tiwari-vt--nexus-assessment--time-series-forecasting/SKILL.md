---
name: time-series-forecasting
description: Covers time series validation (chronological splits, rolling windows, no data leakage), forecast metrics (MAE, MAPE, RMSE, R²), temporal and lag feature engineering, baseline models, and autocorrelation analysis. Use when building or reviewing forecasting pipelines, designing experiments, or validating time series models. Use when this capability is needed.
metadata:
  author: vivek-tiwari-vt
---

# Time Series & Forecasting Expert

## When to Apply

- Splitting or validating time series data
- Computing forecast metrics or designing features
- Implementing or comparing baseline/naive forecasts
- Analyzing autocorrelation or seasonality

## Critical Principles

1. **Never shuffle** time series data—order matters.
2. **Always validate chronologically**—test set must be strictly after train/val.
3. **Avoid look-ahead bias**—no future information in features or normalization.
4. **Account for seasonality**—hour, day, week, month.
5. **Check stationarity**—detrend or difference if needed.
6. **Choose metrics carefully**—MAPE can explode with small actuals.
7. **Validate lag/rolling features**—ensure no leakage (e.g. use `.shift(1)` before rolling).
8. **Plot** predictions vs actuals.

## Validation Best Practices

### Train/Val/Test Split (Chronological)

Split by fractions (e.g. 0.67 / 0.17 / 0.16). Never shuffle. Return `(train, val, test)` DataFrames. Assert `train.timestamp.max() < val.timestamp.min()` and `val.timestamp.max() < test.timestamp.min()`. Log date ranges for each split.

```python
def train_val_test_split(
    df: pd.DataFrame,
    train_frac: float = 0.67,
    val_frac: float = 0.17,
) -> Tuple[pd.DataFrame, pd.DataFrame, pd.DataFrame]:
    n = len(df)
    train_end = int(n * train_frac)
    val_end = int(n * (train_frac + val_frac))
    train = df.iloc[:train_end].copy()
    val = df.iloc[train_end:val_end].copy()
    test = df.iloc[val_end:].copy()
    assert train['timestamp'].max() < val['timestamp'].min()
    assert val['timestamp'].max() < test['timestamp'].min()
    return train, val, test
```

### Rolling-Window Validation

Given `context_window`, `forecast_horizon`, `step_size`: for each start index, take `context = df.iloc[i - context_window:i]`, `target = df.iloc[i:i + forecast_horizon]`. Assert `context.timestamp.max() < target.timestamp.min()`. Return list of `(context, target)` pairs. Slide by `step_size`.

## Metric Computation

Compute MAE, MAPE, RMSE, R² from `predictions` and `actuals`. Validate same length, no NaNs. For MAPE, mask `actuals == 0` and handle all-zero actuals (e.g. return inf or skip). Return `Dict[str, float]`.

```python
# MAE: np.mean(np.abs(predictions - actuals))
# RMSE: np.sqrt(np.mean((predictions - actuals)**2))
# MAPE: mean of |pred - actual| / |actual| over non-zero actuals, scale by 100
# R²: 1 - ss_res / ss_tot
```

## Feature Engineering

### Temporal Features (No Look-Ahead)

From `timestamp_col`: hour, day_of_week, day_of_month, month, week_of_year; is_weekend, is_business_hours, is_peak_evening. Add cyclical: `hour_sin/cos = sin/cos(2*pi*hour/24)`, `day_sin/cos = sin/cos(2*pi*day_of_week/7)`.

### Lag Features (No Look-Ahead)

Use `df[col].shift(lag)` for lags (e.g. 24, 48, 168). For rolling stats use **shifted** series: `df[col].shift(1).rolling(window).mean()` and `.std()`. Log warning when NaNs are introduced; consider dropping leading rows.

## Baseline Models

- **Naive persistence**: forecast = last `horizon` values of context (tomorrow = today).
- **Seasonal naive**: forecast = same time last season (e.g. last 168 values, take last `horizon`).
- **Rolling mean**: smooth last N hours, optionally scale by hourly pattern from history.

## Autocorrelation Analysis

Use `statsmodels.tsa.stattools.acf`, `pacf` with `nlags`. Find lags where `|acf| > 2/sqrt(n)`. Use to choose lag features and ARIMA orders. Log significant lags.

## Checklist

- [ ] Splits are strictly chronological with no overlap
- [ ] All features use only past data (shift before rolling)
- [ ] Metrics handle edge cases (zeros, NaNs, equal length)
- [ ] Baselines implemented for comparison
- [ ] Plots: predictions vs actuals, residuals, ACF/PACF when relevant

## Additional Resources

- For full code examples (rolling validation, metrics, temporal/lag features, baselines, ACF), see [reference.md](reference.md).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vivek-tiwari-vt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
