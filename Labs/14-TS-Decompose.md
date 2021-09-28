> - Dataset: [Monthly Accidental Deaths in USA](https://www.kaggle.com/rakannimer/air-passengers)
> - Colab Notebook of this lab: [Time-series Decomposition](https://colab.research.google.com/drive/1AWP9cytCnsJr8ayUOHj9oIsXOElVJR3L?usp=sharing)


- In Lab [[13-Lab-TS-Spreadsheet]], we had practice to implement MA, SE on spreadsheet. You can also do it in Python, following Section: `Move, Smoothe, evaluate` in this [Kaggle Notebook](https://www.kaggle.com/kashnitsky/topic-9-part-1-time-series-analysis-in-python/notebook#Move,-smoothe,-evaluate) (This will a tasks in [[13-14-Uber-Time-series]])
- Time-series Decomposition in Python

## Libraries
We start the lab with loading important libraries:
```
import pandas as pd
import numpy as np
from statsmodels.tsa.seasonal import seasonal_decompose 
from sklearn.metrics import mean_absolute_error, mean_squared_error
import statsmodels.api as sm
import matplotlib.pyplot as plt
```

- `statsmodels.tsa.seasonal` is used for time-series decomposition
- `statsmodels.api.sm` is used to run the OLS on trendline of the time series

## Work with Time-series in Pandas
- Column `Month` in the `AirPassenger.csv`, would be read as `string` in pandas
- Notice that, without converting to time-series, when we plot it does not show the time-range in `x-axis`

![[lab14.1.png]]

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

![[lab14.2.png]]

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

![[lab14.3.png]]

> Discussion: Multiplicative model performs slightly better, back to the [[13-14-Time-series-Analysis]], it implies what characteristics of the time-series?

## Deep-dive Analysis
> We will deep-dive into the model which performs better `Multiplicative`

From the single orginal time-series, we have decomposed to 3 component series.
Let's have a look on each of the decomposed components. 

### Residuals
If we do a good job in modelling, the remaining residuals are more likely the noises, that `result2.resid` should behave as a noise:
- ==No clear trends (stationary)== -> Plot the series of residuals: `result2.resid.plot()`
	- By the residuals plot, we see that the residuals is higher for the early period (which is reasonable, as we have less preceeding historical data points)
	- At the end of period, we have a "big" residuaks in 1960 (==thanks to the decomposition, we detect this abnormal data points==, while it is not visible in the original time-series plot)
- Check ==the distribution of residuals== -> Normally, we hope that the residuals (noise) will have normal distribution -> Create the histogram: `result2.resid.hist()`

![[lab14.4.png]]

### Seasonality
- Zoom-in to see clearly the pattern of seasonablity
	- Plot the the seasonal component in 1950 and 1951: `result2.seasonal["1950":"1951"].plot()` -> The chart shows the months that enables us to examine the seasonal better
		- We see that the seasonality pattern is quite consistent year-by-year
	- From common (business) sense, it is also intuitive to expect that the volume of airline passenger will vary by month
	- Zoom-in one year `result2.seasonal["1950":"1950"].plot()`
		- The Air Passnger peaks in Jul-Aug (Summer Vacation Season)
		- Drop significantly in Nov-Dec (maybe during Christmas and New Year, people prefer to stay at home)

![[lab14.5.png]]

- With the fact that the pattern of seasonability is consistent year-by-year, and the model is `Multiplicative`:
	- Group by months and Averaging the `seasonal` factor 
	- For each month, we have a `seasonal multiplier`
	- It is called `multiplier` because we will multiply this factor to other time-series component to take into account the seasonability in the prediction
	- ==This will enable us to predict any period in the future== (See: [[#N-month to the future]]). It is an advantage comparing to predict only 1-period ahead in `MA` and `SE` in [[13-Lab-TS-Spreadsheet]].

### Linear Trend Model
Have a look into the `trend` component from the data.

![[lab14.6.png]]

The trend is upward, that motivate us to fit a linear trendline by technique in [[11-12-Regression-Analysis]] like this:

> Discussion: How you will build this trendline?

Linear Trend Model:

$$Y_t = b0 + b1 \times t + \epsilon_t$$

To implement it in Python:

1. Generate the series `t`: `t: range(len(result2.trend)`
2. Regression (OLS): `trend` on `t` and the `constant` (use `statsmodels.api` library)

```
y = df['trend']
X = df['t']
X = sm.add_constant(X)
reg_model = sm.OLS(y, X).fit()
reg_model.summary()
```
> Discussion: Comment on the summary report of Linear Regression
![[lab14.7.png]]

## Recomposed Prediction
The `seasonality multiplier` in [[#Seasonality]] and model in [[#Linear Trend Model]] will give us more freedom to do the prediction.

> It's time to recompose our components.

1. For each period in the series, extract the `month` from the `month_date` columns: ```pred_df.month_date.dt.month```
2. We have `seasonality multiplier` for each of 12 months in a year, extract: 
```s_multi[x['month']]```
4. Finally, multiply the predicted trend from the OLS of [[#Linear Trend Model]] with the `s_multi`: 
```pred_df.pred_trend * pred_df.s_multi```

The output dataframe as follows:
![[lab14.8.png]]

Plot to compare the recomposed prediction with the original time-series:

![[lab14.9.png]]

> Discussion: The MAPE in the `Recomposed Prediction` with `trendline x s_multiplier` (`4.67%`) is slightly higher than `Decomposed` (`2.44%`)

## N-month to the future
As mentioned, the benefit of decomposing to component, and apply some techniques on each component:
- `Trend`: Linear Regression
- `Seasonality`: Groupby-Averaging

Now, we have all we need to predict any period ahead in the future.

> For example, we want to predict the time-series of two-year ahead (24 months)
1. Create the `date_range` for next 24 month (we know beginning date, frequency, and number of periods we want to create)
```pd.date_range(df.index[-1], freq='MS', periods=n_month+1)[1:]```
3. For each of 24 months ahead, we could assign `t` for it
4. With the OLS model, predict the trend: 
```future.t * reg_model.params['t'] + reg_model.params['const']```
5. Assign the `s_multi` based on the month of the period: `s_multi[x['month']]`
6. Recompose: `future.pred_trend * future.s_multi`

Plot the prediction, we can see that it looks quite sensible: 
![[lab14.10.png]]

> As we do not have the observed value of next 24 months, we cannot calculate the evaluation metrics yet. 

## Recap
- Working with time-series in Python
	- `pd.to_datetime()`: convert value to datetime
	- `.reset_index()`: convert datetime columns to the index that Python recognize a series as time-series
	- `pd.date_range()`: to create a range of dates
- Implement the Time-series Decomposition by `seasonal_decompose` from `statsmodels.tsa.seasonal`
- By the decomposition, we seperate one orginal time-series into 3 component time-series
	- `residual`: analyze and understand the behavior of noise (stationary, distribution)
	- `seasonality`: zoom-in, understand the pattern by months
	- `trend`: create the linear trend model with `statsmodels.api.sm`
- Recompose components and predict n-month ahead in the future