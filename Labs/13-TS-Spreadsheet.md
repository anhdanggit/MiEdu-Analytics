> - Dataset: [Monthly Accidental Deaths in USA](https://www.kaggle.com/rakannimer/air-passengers)
> - Google Spreadsheet of this lab: [MA-SE in Spreadsheet](https://docs.google.com/spreadsheets/d/1m_GakxZU7dgJpyvGYFAK07AZ4kVRCFA8zZ5ltxPC8wk/edit?usp=sharing)

## MA in Spreadsheet
In this exercise, we will be **Moving Average** (MA) in Google Spreadsheet. 

### 1 - Time-series line chart
- Copy the data and name the tab `Data`
- Choose 2 columns of the dataset -> `Insert` -> `Chart`
- We have the time-series line chart as in the graph below

![[lab13.1.png]]

> Discussion: Is the `#Passenger` series stationary? 

### 2 - First-Difference
- Create column `diff_Passenger` = `#Passenger_t` - `#Passenger_(t-1)`
- Plot the series of `diff_Passenger`

![[lab13.2.png]]

> Discussion: Is the `diff_Passenger` series stationary? Why the first period is NA? Which series should be used for MA and SE?

### 3 - MA with different windows
In [[13-14-Time-series-Analysis]], we know that MA would take the `k` as the `moving window`
- Create column `MA_3` = AVG(`diff_Passenger_(t-1)` - `diff_Passenger_(t-3)`
- Create `MA_6`, `MA_12` (Note: `k = 2, 3, 6` is arbitrary)

![[lab13.3.png]]

- Plot the time-series of `diff_Passenger` and `MA_2`, `MA_3`, `MA_6`
- You can click on the line to change the format and color
	- Change `MA_2` to Red
	- Change `MA_3` to Orange
	- Change `MA_6` to Green
	- `diff_Passenger` as Blue (Line Opacity = 70%)

![[lab13.4.png]]

- Plot `MA_k` series with the `diff_Passenger`

![[lab13.5.png]]

> Discussion: About how the MA lines change with different $k$?

### 4 - Predict with MA
- Create the column: `pred_y_MA2` = `previous_y` + `MA2`
	- Notice thate the `MA` series is to predict the `diff_Passenger` ($\Delta \hat {y_t}$) not the `#Passenger` ($\hat{y_t}$) 
	- Predicted `#Passenger` at time `t`: $\hat{y_t} = y_{t-1} + \Delta \hat {y_t}$
		- Where:
			- $y_{t-1}$ is observed
			- $\Delta \hat {y_t}$ is `MA_k`
- Predicted value: `pred_y_MA2`, `pred_y_MA3`, `pred_y_MA6`
- Plot `pred_y_MA2`, `pred_y_MA3`, `pred_y_MA6` versus `#Passenger`
- We can see that with the **Moving Average**, our predictions are quite close to the actual.

![[lab13.6.png]]

> Discussion: You guess which MA gives us the best prediction?

### 5 - Evaluation
With the formula of Evaluation Metrics in [[13-14-Time-series-Analysis]]:
- Compute the `MSE` and `RMSE` (between `pred_y_MA2`, `pred_y_MA3`, `pred_y_MA6` versus `#Passenger`)
- Which one is the best MA to predict, in term of `RMSE`, `MSE`?

## SE in Spreadsheet
In this exercise, we will be **Smoothing Exponential** (SE) in Google Spreadsheet. 

> To keep the spreadsheet clean, duplicate the tab `Data` used in [[#MA in Spreadsheet]] for this task

### 1 - SE with different alpha
- Try the `alpha` = 0.2, 0.4, 0.6
- We will implement this formula in Spreadsheet:

$$\hat{Y}_{t+1} = \hat{Y}_t + \alpha({Y}_t - \hat{Y}_t)$$

- For the first value of the series, we put: $Y_1 = Y_0$
- After that, we compute by the formula:
	- $\hat{Y}$: Prediction value - `SE_alpha*`
	- $Y$: Observed value - `diff_Passenger`
		- $Y_2 = Y_1 + \alpha(Y_1 - \hat Y_1)$
		
### 2 - Predict with SE
- Similar to [[#4 - Predict with MA]], `SE_alpha*` values are prediction for `diff_Passenger`
- Prediction: `pred_y_SE_*` = `previous_y` + `SE_alpha*`

### 3 - Evaluation
- Similar to [[#4 - Predict with MA]], we compute `RMSE` and `MSE`
- Then, plot the results

![[lab13.7.png]]

> Discussion: 
> - Comment about the results? 
> - Which alpha is the best? That imply what insights about the behavior of this time-series?
> - Compare MA and SE

## Recap
- We apply the MA and SE with the spreadsheet, with the intuitive and interactive interface of Spreadsheet, we hope you could visualize in your mind how MA and SE works
- By time-series plot, we graphically observe the stationary of time-series
- We de-trend (transform the non-stationary to stationary) with the Differencing techniques
- We apply MA and SE for the stationary time-series (diff) to predict the value
	- Prediction Number of Airline Passenger for next month
	- Compute the evaluation metrics: `RMSE` and `MSE`
	- Plot the prediction and actual value, we see both MA and SE works quite well. SE is little bit better. 
	- We observe how different `k` in moving window of MA, and `alpha` in SE impact the preduction