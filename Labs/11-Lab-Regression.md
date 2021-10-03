> - Dataset: [Ecommerce Customers](https://www.kaggle.com/srolka/ecommerce-customers)
> - Colab Notebook of this lab: [Ecommerce-Regression.ipynb](https://colab.research.google.com/drive/1DvdewmdId2BJHIGL4QkSj0oak9Mfpc6r#scrollTo=7XODuZ1TERhT)

## Data Explore
> You can apply any techniques in [[6-Lab-EDA-Viz]] for this dataset. Though we do it quite briefly in this lab
```
ecommerce_df.describe([0.01,0.05,0.25,0.50, 0.75, 0.90,0.95,0.99]).T
```
- Using the `.describe()` to summarize the data
- Add the argument to report the percentiles of 1%, 5%, ..., 95%, 99%
- `.T` transpose the resulting table to make it easy to see

### `sns.pairplot()`
- Using the `sns.pairplot()` to explore the data
	- On diagonal: histogram of each continuous variables
	- Off-diagonal: scatter plot of pair of continuous variables

![[attachments/lab11/lab11.1.png]]

> Discussion: Comment about the distribution (histogram) and relationships of continuous variables

### Heatmap Correlation Matrix
Similar to what we do in [[6-Lab-EDA-Viz]]
![[attachments/lab11/lab11.2.png]]

> Discussion: Comment about relationship of continuous variables

### `sns.regplot()`
A new thing in this lab: we could run a simple and quick regression analysis between 2 continuous variables by:
```
sns.regplot(y='Yearly Amount Spent',x='Avg. Session Length', data=ecommerce_df)
``` 
This will augment the scatter plot with a regression line to represent the relations between 2 variable (which is the visualization of the Regression model between variable in y-axis and x-axis)

### OPTIONAL: Winsorize Outliers
> This is not the focus of this lab, yet besides the technique to solve outliers in [[6-Lab-EDA-Viz]] to just simply remove them. We try another methods named Winsorize.

- It is simply that replacing the outliers (`>p95`) by the value of `p95`
- Python tricks:
	- Write the function to take: `Dataframe`, `column` and a `cutoff` percentiles, and apply the Winsorize technique on the column (input)
	- Apply the function to all continuous columns in the dataframe, we do it by writing a loop
```
### Winsorize (Replace) Outliers
def replace_outlier(df0, col, cutoff = 0.95):
	df = df0.copy()
	cut = df[col].quantile(cutoff)	
	df.loc[df[col] > cut, col] = cut
	print(col)
	print(cut)
	return df
	
### loop
df1 = ecommerce_df.select_dtypes(['float','int']).copy()
for c in df1.columns:
	df2 = replace_outlier(df1,col=c)
```
- As the output of replacing outliers we have the histogram before/after as below:
![[attachments/lab11/lab11.3.png]]
> Discussion: Comment about before/after histogram. Should we apply the winsorize on our dataset?

## Linear Regression Model
### Evaluation Metrics
> See: Evaluation Metrics in [[../Tutorials/11-12-Regression-Analysis]]

We construct them in Python as below:
```
def print_evaluate(true, predicted):
	mae = metrics.mean_absolute_error(true, predicted)
	mse = metrics.mean_squared_error(true, predicted)
	rmse = np.sqrt(metrics.mean_squared_error(true, predicted))
	r2_square = metrics.r2_score(true, predicted)
	print('MAE: {:,.2f}'.format(mae))
	print('MSE: {:,.2f}'.format(mse))
	print('RMSE: {:,.2f}'.format(rmse))
	print('R2 Square: {:,.2f}'.format(r2_square))
```
### Fit the Model
```
df_reg = ecommerce_df.select_dtypes(['float','int'])
Xs = df_reg[[x for x in df_reg.columns if x ! = 'Yearly Amount Spent']]
y = df_reg['Yearly Amount Spent']
Xs = sm.add_constant(Xs)

est = sm.OLS(y, Xs).fit()

# print the result
print(est.summary())
```
- Filter out continuous variables only
- `Xs`: all other continuous variables (except the `y` - dependent one of our model: `Yearly Amount Spent`)
- `y`: `Yearly Amount Spent`
- We fit the model by the library `import statsmodels.api as sm`:
```
est = sm.OLS(y, Xs).fit()
```
### Interpret Results
- This is the result: With the scope of this course, the details in red is what we should focus and discuss
![[attachments/lab11/lab11.5.png]]
- One value of Regression Analysis is that we can estimate the size and direction of impacts on each variable on `y`
- Notice the `coef` in the report: it shows us the impact of each variable on `Yearly Amount Spent`
	- `const` (Intercept): We add this to our model by `Xs = sm.add_constant(Xs)` in [[#Fit the Model]]. Normally, `const` does not have much insights, it enables us to not force the regression line cross the origin `(0,0)` so that the model is more flexible
	- For each variable: `coef`: the marginal effect of a variable in `y`
		- `coef` of `Length of Membership` increase by 1 unit we can incrase the `Yearly Amount Spent` of that users by `$61`
		- `coef` of `Avg. Session Length` increase by 1 unit we can incrase the `Yearly Amount Spent` of that users by `$25`
		- What it means to business?
			- This regression analysis helps us to quantify the impacts, and from that can quantify the value of different actions (help them in prioritizaton)
			- For example: To increase the `Length of Membership`, the business can offer the rewards for loyal users, to incentize them to stay with us
				- We can measure how this offer can prolong the length of membership (could apply [[../Tutorials/10-AB-Testing]]) -> `changes in Length of Membership` 
				- By this change in units of length of membership, we can use the `coef` from Regression Analysis to estimate, how much `Yearly Amount Spent` we can uplift

> Discussion: Interpret other coef

![[attachments/lab11/lab11.6.png]]
- Now, look at `P-val` and `[0.025, 0.975]`: 
	- `coef` by the nature is the estimate of impact, hence it should not be presented as a single value without noticing the uncertainty of this estimates
	- `[0.025, 0.975]`: the report presents for us the interval from `p2.5%` and `p97.5%` distributions of each `coef` (this is calculate by `std err`, `coef` as mean, and the assumption about `coef` distribution)
		- **Significant coef**: is the one with the range not include `zero` (significant at 5%)
		- More generally, we have the `t` (t-val) to measure how different the `coef` from zero and `p-val` for this t-val -> This is very similar to the concept of hypothesis testing in [[../Tutorials/10-AB-Testing]]
		- If `p-val` small, we confidently say the `coef` is statistically significant
		
> Discussion: What is the null hypothesis for each p-val in the report?

- Lastly, we have `R-squared` to measure the fitness of model:
	- By [mathematical constrution](https://www.investopedia.com/terms/r/r-squared.asp), it measure how many percent of the variance of `y` is explained by the model?
	- The closer the `R-squared` to 1, the better the model is

### Model Performance
After interpreting and extract the insight from the model of Regression Analysis, we are still interested in the performance of it, by [[#Evaluation Metrics]]

![[attachments/lab11/lab11.7.png]]

- We build a good model, which explain 98% of the variance of `Yearly Spent Amount`
- Visually, we could check by plot the scatter plot of the `Yearly Amount Spent` and the prediction value. 
	-  We expect two value are approximate: `Yearly Amount Spent` = `Linear Prediction Value`
	-  So, in the scatter plot, the dots should be on the diagonal line (`y = x`)

## K-means + Regression
> In this section, we will learn how to combine the result from Kmeans to the Regression Model.

### K-means
- Similar to step-by-step in [[8-Lab-Kmeans]]
	- Normalize variables: `Avg. Session Length`, `Time on App`, `Time on Website`, `Length of Membership`
	- Kmean clustering into 3 clusters
- We have the distribution of cluster as below
![[attachments/lab11/lab11.8.png]]
- By clusters, we could do futher analysis. For examle: `regplot` by different clusters.
	- The slope of regression line is slightly different for different subsets
	- As the scope is not much different, the combinations of k-means cluster to Linear Regression does not generate much value
	- But, if the scope is very different across subsets, if we lumpsum all subsets together and fit the regression, we will ==force== same slops for different subsets -> In that case, the addition of k-mean information to Linear Regression will improve the results. 
	
![[attachments/lab11/lab11.9.png]]


> Though the `regplot` graph hints us that the addition of k-mean will not change the performance of Linear Regression much, we will conduct the procedure (for the purpose of illustration)

### Categorical to Numeric
- `Cluster` is supposed to be categorical (though in pandas, they might present as numeric)
- We follow the steps in [[6-Lab-EDA-Viz]] to convert `cluster` to one-hot code

```
onehot_df = pd.get_dummies(ecommerce_df['cluster'])
onehot_df.columns = ['cluster_' + str(n) for n in onehot_df.columns]
```
![[attachments/lab11/lab11.10.png]]

- Merge the one-hot code to the dataframe, below are all regressors we have for the model

![[attachments/lab11/lab11.11.png]]

### Fit the model
```
Xs = df_kmeans_reg[[x for x in df_kmeans_reg.columns if x != 'Yearly Amount Spent']]
y = df_kmeans_reg['Yearly Amount Spent']

Xs = sm.add_constant(Xs)
est_kmeans_reg = sm.OLS(y, Xs).fit()

# print the result
print(est_kmeans_reg.summary())
```
![[attachments/lab11/lab11.12.png]]

> Discussion: Intepret the results

### Model Performance
![[attachments/lab11/lab11.13.png]]

## Recap
- Data Exploration before running the Regression Analysis
	- `sns.pairplot()`
	- Heatmap Correlation
	- Winsorize Outliers
- Linear Regressions:
	- Evaluation Metrics: `MAE`, `MSE`,`RMSE`,`R-squared`
	- Fit the model with `sm.OLS()` from `statsmodels.api`
	- Read the model results to interpret the size of impacts on the outcome
- Combine the K-mean (learnt in [[8-Lab-Kmeans]]) with the Linear Regression
	- Construct the cluster by k-means
	- Convert to one-hot code
	- Add one-hot code dummies as regressions of Linear Regression Model