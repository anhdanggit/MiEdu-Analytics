A **Time-series** is a set of observations on a quantitative variable collected over time (For example: Spending of a user over time, Daily Revenue over time).

- Most businesses keep track of a number of time series variables
- Include daily, weekly, monthly, quarterly figures on sales, costs, profit, customer counts, orders, etc.
- Businesses are often interested in forecasting future values of a time series
- Different from the `explanaroty analysis` [[11-12-Regression-Analysis]], with a target (dependent) variable, in Time-series modeling we explain and forecast a variables based on its own historical value -> `extrapolation`

## Time-series Method
The general from of the extrapolation model in time-series:

$$Y_{t+1} = f(Y_t, Y_{t-1}, Y_{t-2}, ...) + \epsilon$$

Where:
- $Y_{t+1}$: the predicted value in time period $t+1$ (future)
- $Y_t$: the value observed at $t$ (current)
- $Y_{t-k}$: historical value at $t_j$ (history)

### Stationary Models
Many time-series methods (such as: [[#Moving Average]] and [[#Exponential Smoothing]]) applies for **stationary** time-series.

- An "informal" way to explain *Stationary Time-series* is that: The time-series have no strong (upward/downward) trends of level over time. 
- Visually, we can compare the stationary and non-stationary time-series in the below charts:
	- *"Light blue" time-series*: `Not stationary`, as the trend is clearly upward
	- *"Dark blue" time-series*: `Stationary`, as the time-series was moving around the `mean = 0`

- How to turn `Non-Stationary` to `Stationary` Time-series -> **De-trend**
	- Take the difference: $Y_{t} - Y{t-1}$
		- The chart above also illustrate the de-trend effect of differencing: The "dark blue" series is the difference of "light blue"
		- We can difference the difference, if after the first difference, the series is still not stationary
- In the method of [[#Time-series Decomposition]], it will automately handle this trend-effect.

![[tutorial13-14.1.png]]

### Evaluation Metrics
- It's impossible to know in advance which method will be the most effective for a given set of data
- A common appraoch is to try different techniques, and evaluate. 
- Common Evaluation Metrics (similar to [[11-Lab-Regression]]):
	- `MAD`: Mean Absolute Deviation
	- `MAPE`: Mean Absolute Percent Error
	- `MSE`: Mean Square Error
	- `RMSE`: Root Mean Square Error

![[tutorial13-14.2.png]]

## Moving Average (MA)
> `Moving Average (MA)` techniques probably the easiest method
- $\hat{Y}_{t+1}$: predicted value at time $t+1$, is simply the average of $k$ previous observations in the series:

$$\hat{Y}_{t+1} = \frac{Y_t + Y_{t-1}+...+Y_{t-k+1}}{k}$$

![[tutorial13-14.3.png]]

- We need to identify `k` one simple way to do it is `Elbow` as in [[8-Lab-Kmeans]] (compare the [[#Evaluation Metrics]] with different value of $k$)

## Exponential Smoothing
> Exponential Smoothing is another averaging technique for stationary data (that works quite similary to [[#Moving Average MA]]), but it allows us to simply assign lower weights to older dta.

- One drawback of MA is that all past data ($k$ previous periods) have the same weight (= $\frac{1}{k}$). 
	- Intuitively, we migh exect that more recent period $t$ and $t-1$ should have more impact on the prediction than the periods long before in the history $t-365$

![[tutorial13-14.4.png]]

- *Exponential Smoothing* should assign higher weight (higher impact) to more recent data points, the weights is diminshing exponentially back to the past (as the older data add very little value in explaining the future data):

$$\hat{Y}_{t+1} = \hat{Y}_t + \alpha({Y}_t - \hat{Y}_t)$$

- The above formula is mathematically equivalent to (Hint: Replace $\hat{Y}_{t} = \hat{Y}_{t-1} + \alpha({Y}_{t-1} - \hat{Y}_{t-1})$, recursively):

$$\hat{Y}_{t+1} = \alpha Y_t + \alpha (1-\alpha) Y_{t-1} + ... + \alpha (1-\alpha)^n Y_{t-n} $$

- With $0 \leq \alpha \leq 1$
	- $Y_t$ have the heaviest weight ($\alpha$)
	- $Y_{t-1}$ have the lower weight the $Y_t$, with $\alpha (1-\alpha)$
	- The weight exponentialy get smaller for older time-period

![[tutorial13-14.5.png]]

> The analytic tools will calculate the value of $\alpha$ to optimize the [[#Evaluation Metrics]]

- With small value of $\alpha$:
	-  Less heavy weight for most recent $Y_t$, the forecast do not react quickly to changes in the data
	-	The chart below compare the Exponential Smoothing with `alpha=0.1` (flat, less responsive), and `alpha=0.9` which is more responsive
	
![[tutorial13-14.6.png]]

## Time-series Decomposition
> Time-series decomposition method involves thinking of a time series as a component of level, trend, seasonality, and noise components. 

- While Classic [[#Moving Average MA]] and [[#Exponential Smoothing]] does not work well with non-stationary (`trend`) and Seasonability, we introduce **Time-series Decomposition Method**, which is a conveninent tool to deal with those cases.

- Time-series of Airline Passengers
![[tutorial13-14.7.png]]
- Decomposition of Airline Passenger
![[tutorial13-14.8.png]]

### Time-series Components:
- `Level`: The average value of the series
- `Trend`: the increasing or decreasing value over time
- `Seasonality`: the repearting short-term cycle in the series
- `Noise`: random variation in the series

> Many tools and Python libary will automatically decompose the time-series for you
	
### Combine Components
- Additive model: $$y(t) = Level + Trend + Seasonality + Noise$$ 
	- Assumption: Linear - the changes and contributes of each components are consistently have the same amount
		- *Linear Trend* is a straight line
		- *Linear Seasonability* has the same frequency (width of cycle) and amplitude (height of cycles)

- Multiplicative model: $$y(t) = Level \times Trend \times Seasonality \times Noise$$
	- Nonlinear (Quadratic or exponential)
	- Changes is not constant
		- *Linear Trend* is a curved line
		- *Linear Seasonability* has the increasing/decreasing frequency (width of cycle) and/or amplitude (height of cycles)

![[tutorial13-14.9.png]]

![[tutorial13-14.10.png]]

### Decomposition as a Analytic Tools
- As an analysis tool, it is useful to inform the behavior of time-series.
- By decomposition into components, it provides a structured way of thinking about time-series
- Real-world problem is messy and noisy, we may and may not be able to cleanly and perfectly break down the time-series into components
- But, this method provide a simple framework to analyze time-series data

## Labs
- [[13-TS-Spreadsheet]]
- [[14-TS-Decompose]]

## Exercise
[[13-14-Uber-Time-series]]

## Reference
Some other popular methods:
- [Time-series Forecasting with Prophet](https://docs.exploratory.io/analytics/forecasting)
- [ARIMA](https://docs.exploratory.io/analytics/arima)