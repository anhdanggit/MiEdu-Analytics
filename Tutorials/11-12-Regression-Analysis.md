> Follow the [[10-AB-Testing]], we introduce another popular and powerful techniques in explanatory analysis: Regression Analysis.

## Correlation Analysis
### Pearson: Continuous vs. Continuous 
![[tutorial11-12.1.png]]
- To measure the correlation between 2 continuous (numerical) metrics, we use the `Pearson Correlation`
- It is used in the `Pairwise Correlation Metrics` in [[6-Lab-EDA-Viz]]
- Take the value between `-1` and `1`
	- Closer to `1`: ==Positive correlation==, when `X` increase, `Y` increases -> In the chart above, we present the correlation between `Customer Value` and `Monthly Premium Auto`, graphically positive correlation will be an upward line (`Corr (Pearson) = 0.39`)
	- Close to `-1`: ==Negative Correlation== -> Downward line
	- Close to `0`: It will be a line with flat-trend (no upward, no doward) (`Corr (Pearson) <= 0.01`)
	
### KW Test: Continuous vs. Categorical 
![[tutorial11-12.2.png]]
- See: [KW Test in Exploratory.io](https://docs.exploratory.io/analytics/kruskal_wallis)
- If you run the *Correlation Analysis* on `Exploratory.io`, it will auto-detect the datatype and present the result differently
- *Pearson Correlation* is applicable for 2 numerical variable
- Run the correlation analysis for numerical vs. categorical analysis:
	- Visualize by the distribution of target varaible (In the graph: `Customer Value`) as boxplot by different values of categorical analysis (`Education`)
	- To statistically testing the difference in visuals, we apply the statistics test
	- `KW Test`: test if the distribution among different subgroups are significantly difference (the overlap in boxplot is small)? -> `P-value`

> Discussion: Guess the Null Hypothesis of `KW Test`, interpret the P-val? (Remind: [[10-AB-Testing]])

### Chi-square Test: Categorical vs. Categorical
- `exploratory.io` does not provide the option to run correlation analysis for 2 categorical variables

![[tutorial11-12.3.png]]

- Yet, you can apply `Chi-squared Test`, similar to the idea of compare the proportions in AB Testing [[10-Lab-ABTesting]]:
	- With 2 categorical variables, we generate the `Contingency table` (example as above)
	- `Chi-squared Test` is applied to compare if the proportions of one categorical metrics (Male and Female) are differrent among subgroups of the other categorical (`Interest`)
	- See: [Statistics 101 Notebook](https://github.com/mieduai/fsds-courses/blob/master/03-Data-Analysis/06-statistic.ipynb)

> Discussion: What's the null hypothesis? Interpret the P-val?

### Non-Linear Relationship
> Does it mean that the pair of metrics with low correlation has no relationship (and we skip low-correlated variables)?

![[tutorial11-12.4.png]]
- We analyze the relationship between Baby Born Weight and Age of Mom and Dad (Dataset: [HERE](https://exploratory.io/data/kanaugust/US-Baby-Data-Clean-Yvg1lvO2Us))
- The result above is obtained in `exploratory.io` (After removing outlier)
- Notice that the `baby_weight` and `mother_age` and `father_age` have very small Correlation (`0.06` and `0.04`)

> Correlation measure the linear relationship, not non-linear.

- Here, by the graph, both `mother_age` and `father_age` have U-curve (non-linear)
	- Give birth when parents are too young is not good (not ready physically, financially)
	- So, when the age of parents increase, the weight of baby incrases
	- Till the age of 30+ for both female and male, the increase in age of parents associate with the decrease in weight of baby (parents pass the optimal age for giving birth)
- The U-curve relationship with the optimal value as in this example is very common in reality
	- This means that blindly rely on correlation might lead to mistake in analysis, as they do not reflect the non-linear relationship
	- That's why we need to repeat and repeat again domain knowledge, intuition, business understandings (as in [[3-Hypothesis-based Approach]]) is crucial (to validate and justify what the metrics and tests told us)
- Indeed, the information about `mother_age` and `father_age` is meaningful to explain and predict the baby weight
- How to deal with it? 
	- Data Processing:
		- Turn `Numerical` to `Categorical` (by discretization techniques as in [[6-Lab-EDA-Viz]])
		- Add Polynomial Features (See: [Sklearn Polynomial Features](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.PolynomialFeatures.html))
	- Using non-linear methods (Not in this course, but you can read about [Decision Tree](https://docs.exploratory.io/analytics/decision_tree) and run this analysis on `exploratory.io`), [GAM](https://en.wikipedia.org/wiki/Generalized_additive_model)

> Correlation Analysis help us to identify and quantify the correlation between two metrics. Though in many context, we would like to see the impacts of multi-factors on the outcomes. Also, rather than the correlation metrics between -1 to 1, we wants to quantify the size of effect: In particular, if we increase one factor by X units, how the outcome metrics is expected to change?


## Linear Regression
**Regression Analysis** and Regression modesl are frequently used to model the relationships between attributes and the outcomes. 
Generally speaking, *regression analysis* estimates the coefficients of each explanatory variables on the outcome. By that:
- We can analyze which factor impact the outcome the most (by the magnitude and significance of coefficients)
- Estimate/Predict the outcomes from the set of explanatory attributes.

One frequent used form of regression analysis is linear regressions, as the name: the output is represent by the mathematical formula, which is the linear combinations of the features:

$$Y = b0 + b1 \times X1 + b2 \times X2 + ... + \epsilon$$

- $Y$: Outcome variables
- $X_j$: Explanatory variables
- $b0$: intercepts
- $b_j$: coefficients
- $\epsilon$: With modelling, we expect to obtain the best approximates of the output values, so there will also a "gap" of the estimates with the actual observed value of $Y$ -> Error term: $\epsilon$

The simple linear regression, having only one explanatory variable: $Y = b0 + b1 \times X1 + \epsilon$

If you plot the relationship of $X1$ and $Y$, we will have the graph as below:

![[tutorial11-12.5.png]]

- Black points are data points, with $X1$ on `x-axis` and $Y$ on `y-axis`
- The blue line is the *fitted* (or estimates/predicts): $a + b1 \times X1$, with $b1$ is the slope of the line (green line)
	- By the slope, we interpret that: ==1 units increase in x will leads to `b1` units increase in `y`==
	- `b0` is the intercept of the line, where the line crossed the y-axis (or value when `x = 0`)
- $\epsilon$: Error term is the (red) gap between `fitted` (blue line) and the actual observed value of `Y` (black dots) 

> Discussion: `coefficients` are the output of the Linear Regression Exercise. How should we estimate those `coefficients` for the `Best-fit` regression model?
> 1. Try to apply the knowledge from [[4-Metrics Design]], use your creativity, how should we design the metrics to measure the "fitness" of model?
> 2. Any shortcomings with the metrics you propose?

- **Least Square**:
	- Linear Regression is also called Least Square, because the coefficients are obtained by choosing the set of coefficients to minimize (least) the sum of squares of error
	
> Discussion: Why it is `squares`?

## Logistic Regression
- When your target variable is binary (Yes/No, 0 or 1), **Logistic Regression** should be used.
	- The output of estimate would be the probability of `Yes`, the value will be in the range between 0 and 1
	- For example, we model the conversions (being converted = 1, else = 0) (as in [[4-Lab-Metrics]]) -> Probability of conversion: `0.7`, means we have 70% likelihood that a particular user will be converted.
- Why Standard Linear Regression as in [[#Linear Regression]] might not work?
	- In the chart below, you can see that if we blindly apply the `Linear Regression` -> The output of the linear regression could go beyond 1 and below 0. This means that: For some cases, we have the probability of convert `> 1` and `< 0` -> Doesn't make sense
	- `Logistic Regression` does the trick by reusing the same linear combination of `X`, and input it into a function `f` to map a output of linear combination to the range from 0 and 1 (as in the graph)

![[tutorial11-12.6.png]]

- Mathematically, this is how it looks like:

$$Y = f(b0 + b1 \times X1 + b2 \times X2 + ...) + \epsilon$$

- Function `f` have the form: $f(z) = \frac{1}{1 + exp(z)}$
	- Replace: $z = b0 + b1 \times X1 + b2 \times X2 + ...$
- By transforming $f(z)$, we will also have:

$$log(\frac{P(y=1)}{1 - P(y=1)}) = b0 + b1 \times X1 + b2 \times X2 + ...$$

- On the Left Side (LS): $log(\frac{P(y=1)}{1 - P(y=1)})$, we have the log of odds of "YES", which represents the ratio between the probability of "YES" and the probablity of "NO"
	- Different from probability only takes value from 0 to 1, odds can take any positive value, and log-odds can takes negative values
	- With X-axis of value 0-1 of probability, by log-odds we can map it to a wider range of value below 0 and above 1 (from `-6` to `6`, in y-axis)
	- The curve of the log-odds called `logit curve`, look as follows:

![[tutorial11-12.7.png]]
	
- The form of $f(z) = \frac{1}{1 + exp(z)}$ is simply the reverse of log-odd, that we will map `-6` to `6` (output of the linear combination) to the range of probability from `0` to `1`

This is useful to analyze any question: 
- Reponse (or not response) analysis in marketing
- Churn (or not churn) analysis
- Subscribe (or not subscribe) analyse
- Continuous Variable (`Customer Value`) could be converted to Binary (High-Value and Not High-Value users) to run the analysis

## Lab
- [[11-Lab-Regression]]
- [[12-Lab-Logit]]

## Exercise 
[[11-12-Uber-Regression]]
