> - Dataset: [Monthly Accidental Deaths in USA](https://www.kaggle.com/rakannimer/air-passengers)
> - Colab Notebook of this lab: [Time-series Decomposition](https://colab.research.google.com/drive/1AWP9cytCnsJr8ayUOHj9oIsXOElVJR3L?usp=sharing)


- In Lab [[13-Lab-TS-Spreadsheet]], we had practice to implement MA, SE on spreadsheet. You can also do it in Python, following Section: `Move, Smoothe, evaluate` in this [Kaggle Notebook](https://www.kaggle.com/kashnitsky/topic-9-part-1-time-series-analysis-in-python/notebook#Move,-smoothe,-evaluate) (This will a tasks in [[13-14-Uber-Time-series]])
- Time-series Decomposition in Python

## Library
We start the lab with loading important library:
```
import pandas as pd
import numpy as np
from statsmodels.tsa.seasonal import seasonal_decompose 
from sklearn.metrics import median_absolute_error, mean_squared_error
import statsmodels.api as sm
import matplotlib.pyplot as plt
```

- `statsmodels.tsa.seasonal` is used for time-series decomposition
- `statsmodels.api.sm` is used to run the OLS on trendline of the time series

## Work with Time-series in Pandas
- Column `Month` in the `AirPassenger.csv`, would be read as `string` in pandas
- Notice that, without converting to time-series, when we plot it does not show the time-range in `x-axis`

![[Pasted image 20210928015650.png]]

- Use `pd.to_datetime()` 

```
df['month_date'] = pd.to_datetime(df.Month, format='%Y-%m', errors='coerce')
```
- Set the datetime columns as index of the DF

```
df = df.reset_index(drop=True).set_index('month_date')
```
- Now, plot it again, it starts to recognize the time-series

## Time-series Decomposition
Using the `seasonal_decompose` from `statsmodels.tsa.seasonal`:

> Remember that, we need to specify the option to run 'additive' or 'multiplicative' options (later we will evaluate)

```
result1 = seasonal_decompose(series, model='additive')
result1.plot()
```

![[Pasted image 20210928020146.png]]

> Discussion: Quick look into the chart, do you have any comment? 

- You can access to each components of the decompositions (as in [[13-14-Time-series-Analysis]]):
	- `Trend`: `result.trend`
	- `Seasonal`: `result.seasonal`
	- `Residuals`: `result.resid`
- Obtain the observed (orginal data): `result.observed`

## Evaluation
We have two model of *Additive* and *Multiplicative*.
- Prediction:
	- Additive: `result.trend + result.seasonal`
	- Multiplicative: `result.trend * result.seasonal`
- Compare by `RMSE`, `MAE`, `MAPE` (from `sklearn.metrics`)
	- Need to combine the time-series, and remove `NA` (by the graph, you could see that the output from `Decomposition` have some missing gap at the head and tail)
	- We will only compare the pair of actual and predict that we have the value for both

![[Pasted image 20210928021313.png]]

> Discussion: Multiplicative model performs slightly better, back to the [[13-14-Time-series-Analysis]], it implies what characteristics of the time-series?

## Deep-dive
> We will deep-dive into the model which performs better `Multiplicative`

### Residuals

### Seasonality

### Linear Trend Model

## Recomposed Prediction

## N-month to the future

> Discussion: The MAPE in the `Recomposed Prediction` with `trendline x s_multiplier` (`4.67%`) is slightly higher than `Decomposed` (`2.44%`)

## Recap