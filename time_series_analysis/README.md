Classical time series forecasting methods:

1. Autoregression (AR)
2. Moving Average (MA)
3. Autoregressive Moving Average (ARMA)
4. Autoregressive Integrated Moving Average (ARIMA)
5. Seasonal Autoregressive Integrated Moving Average (SARIMA)
6. Seasonal Autoregressive Integrated Moving Average with Exogenous Regressors (SARIMAX)
7. Vector Autoregression (VAR)
8. Vector Autoregression Moving Average (VARMA)
9. Vector Autoregression Moving Average wit Exogenous Regressors (VARMAX)
10. Simple Exponential Smoothing (SES)
11. Holt Winter's Exponential Smoothing (HWES)

---

## Forecast quality metrics

Before we begin forecasting, let's understand how to measure the quality of our predictions and take a look at the most commonly used metrics.

**`R squared`**: 

coefficient of determination (in econometrics, this can be interpreted as the percentage of variance explained by the model), (-\infty, 1]

R^2 = 1 - \frac{SS_{res}}{SS_{tot}}

```python
sklearn.metrics.r2_score
```

**`Mean Absolute Error`**:

this is an interpretable metric because it has the same unit of measurement as the initial series, [0, +\infty)

MAE = \frac{\sum\limits_{i=1}^{n} |y_i - \hat{y}_i|}{n}

```python
sklearn.metrics.mean_absolute_error
```

**`Median Absolute Error`**:

again, an interpretable metric that is particularly interesting because it is robust to outliers, [0, +\infty)

MedAE = median(|y_1 - \hat{y}_1|, ... , |y_n - \hat{y}_n|)

```python
sklearn.metrics.median_absolute_error
```

**`Mean Squared Error`**:

the most commonly used metric that gives a higher penalty to large erors and vice versa, [0, +\infty)

MSE = \frac{1}{n}\sum\limits_{i=1}^{n} (y_i - \hat{y}_i)^2

```python
sklearn.metrics.mean_squared_error
```

**`Mean Squared Logarithmic Error`**:

practically, this is the same as MSE, but we take the logarithm of the series. As a result, we give more weight to small mistakes as well. This is usually used when the data has exponential trends, [0, +\infty)

MSLE = \frac{1}{n}\sum\limits_{i=1}^{n} (log(1+y_i) - log(1+\hat{y}_i))^2

```python
sklearn.metrics.mean_squared_log_error
```

**`Mean Absolute Percentage Error`**:

this is the same as MAE but is computed as a percentage, which is very convenient when you want to explain the quality of the model to management, $[0, +\infty)$

MAPE = \frac{100}{n}\sum\limits_{i=1}^{n} \frac{|y_i - \hat{y}_i|}{y_i}

```python
def mean_absolute_percentage_error(y_true, y_pred): 
    return np.mean(np.abs((y_true - y_pred) / y_true)) * 100
```