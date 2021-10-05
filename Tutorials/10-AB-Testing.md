> In [[10-AB-Testing]] and [[11-12-Regression-Analysis]], we will go beyond the ==descriptive analysis==: `GROUP BY - summarize` in [[1-2-DWH & SQL]] and EDA techniques in [[6-Lab-EDA-Viz]] - to ==exaplanatory analysis==, in which we analyze and understand the key factors that migh impact (highly and significantly correlated) the desired outcomes. 

![[tutorial10.0.png]]

AB testing plays a critical role in decision-making processes across various industry. This method is to compare and test the effectiveness and impact of two different business strategies. 

While Regression Analysis in [[11-12-Regression-Analysis]] is based on historical (observational data), AB testing is experimental, which means that the plan to testing, hypothesis is designed beforehand, then we start collecting data.

## AB Testing Settings
![[tutorial10.1.png]]
In a typical AB testing setting, we create and test 2+ variants and check the effectiveness and performance metrics
- **Decision**: For example, we have 2 versions of web landing pages, ==we would like to know which version will bring us more visitor sign up== -> So that we will change to the better version of sign-up button in our website.
- **Hypothesis**: `Variant A` (Sign-up in Red Button) will get more attetion and have higher sign-up
- **AB Testing Settings**: 
	- `Testing Period`: Select the period of testing (Start Date - End Date). Notice the seasonality and nature of product to choose it, for example website have a peak of users in weekend, we should design the testing period to cover several weeks 
		- ==Longer period will help us to normalize time-effect==, but it will be more costly to implement the test (need to wait longer, and spend more money to acquire users)
		- The length of testing period will also determine the sample size that we have (`Total customers` = `Average Daily Customers` x `Days of Testing Period`)
	- `Randomize`: Randomly assign half of customers in group A, and the other half to group B. This is enabled by the config in website or apps
	- `Key metrics`: To compare between 2 variants, it could be any metrics in [[4-Metrics Design]], such as: Conversions (SignUp rate, Subscription Rate), Retention, Average Daily Revenue, Conversion

## AB Data & Analyze Experiment Results
- Once we finish the testing period in [[#AB Testing Settings]], we will have the dataset with this information:
	- `date` 
	- `landingPagePath`: different for Variant A and B
	- `uniquePageView`: Total number of users assigned to each variant
	- `signUpCount`: Number of users click to signing up (Notice that `signUpCount` < `uniquePageView`)
	![[tutorial10.2.png]]

> Now, we will analyze the data to answer the question: If the Variant A (Sign-up button in Red) is better?

- By simply plot the line chart comparing the `signUpCount` of 2 settings, we could easily have a easy conclusion: ==Variant A (blue line) consistenly performs better than B (orange line)==
![[tutorial10.3.png]]
- In reality, the result is not simple to say. The difference between Variant A and B could be very subtle ![[tutorial10.4.png]]
	- By looking this chart, it's difficult say. We can say that A seems to be better than B, but we are not confident. The gap might appear by chance, rather the actual impact.
	- ==Then, we need to test the hypothesis that there is a diffirence between Variant A and B, and that difference is statistically significant==
	
## Statistical Hypothesis Testing
There is a heavy theoretical foundation for the statistical hypothesis, yet due within the scope of this tutorial, we cannot cover it. Thus, we will focus how intuitively interpret the results of statistical hypothesis testing. 

### Results Display
Most of popular tools for Web & Apps Analytics ([Firebase](https://en.wikipedia.org/wiki/Firebase), [Google Analytics](https://analytics.google.com/analytics/web/)) will provide the options to:
1. Implement [[#AB Testing Settings]]
2. Run the statistical test behind-the-scence for you
3. Visualize/Report the results (friendly to users with limited background in maths and statistics)
4. Enable users to interact and do further analysis on the result

Below is the standard report of AB results:
![[tutorial10.5.png]]

- The results compare 5 variants of displaying the price in a website: 
	- (1) Original (Baseline - in Gray)
	- (2) Price without zeros (Green)
	- (3) Red previous price (Red)
	- (4) Green sale price (Blue)
	- (5) Bigger Price (Blue)

> Discussion: Look into the results, what we can conclude? Which variant gives the best result?
- The key metrics over there is `Pageviews per session` (Hypothesis: The display of price will trigger users to click more to explore -> Higher pageviews per session)
- Improvement: 
	- `+15.6% to +34.9%`, by the statistical technique, it's estimate that the variant will increase the `Pageviews per session` by 15.6%-34.9%, comparing to the baseline 
	- The estimate is a range with (commonly) 95% of confidence:
		- `Green`: the range does not cover 0%, and positive -> We are confident that the result is signifcant (far from zero)
		- `Red`: the range does not cover 0%, and negative
		- The range covers 0% -> We cannot conclude
- Visually, the range of improvements is present as boxplot (See: [[6-Lab-EDA-Viz]])
	- `Green`/`Red`: Distribution does not overlap the baseline distribution
	- The distribution might be overlapped with a long-tailed (but for majority of distribution - 'box', does not overlap)
- Line plot of `Pages per session over time` over time, for different variants.

> The significance means: The observed difference in data is large enough, so we conclude that the variants create meaningful changes.

### Null Hypothesis
- `Null hypothesis` (H0) is the tool that enable us to conveniently develop the statistics foundation
> Fun fact: Null hypothesis has the sad story, as it's the hypothesis constructed to be rejected.
- For example, if you want to validate the difference between Group A and B, you will set the `null hypothesis` that there is no difference between them :(
- The trick here is that: We don't prove something true, yet we prove that we have statistical evidence to reject the opposite -> So, some cautious statistican will not say: Hypothesis A is right, but they will say: We fail to reject Hypothesis A.

### Statistics Behind

#### Frequentist
- In short, AB testing is seeking for statistically significant differences between variants
- There are different statistic tests, but in general, they attempt to measure "How difference of what we observed" -> Then, summarize it by some statistic metrics
	- For example, `t-value` to compare the difference in mean of the outcome metrics of Variant 1 and 2: `M1` and `M2`
	$t = \frac{M1 - M2}{\sqrt{\frac{S1^2}{N1} + \frac{S2^2}{N2}}}$
	- How to understand this formula?
		- Difference in Mean of 2 groups: `M1 - M2`
		- We need to normalize it by how spread the data (the more spread distribution, will make it more difficult to interpret the result): `S1`, `S2` as standard deviation
		- Sample size matter: `N1` and `N2` are numbe of 2 samples
	- The bigger `t-value`, the more difference in average value of 2 groups
		- Now, how the variance of each factor in the component will impact the "Measure of difference"
			- The absolute value of Difference itself (as the chart in [[#AB Data Analyze Experiment Results]])
			- More spread distribution (higher standard deviation) will make us less confident. That's why `S1` and `S2` appears in the denominator (the larged deviation, the smaller `t-value`)
			- If we observe more and have bigger sample size, we can improve the confidence (and compensate the uncertainty of standard deviaion). `N1` and `N2` appears in the denominator
- Why it's named "frequentist?"
	- For each statistic test, it will come with the (theoretical) distribution of statistics value. For example, `t-value` will have the `t-distribution`
	- This distribution will remind us about the histogram (`value` x `frequency`) in [[6-Lab-EDA-Viz]]
	- Many statistical concepts is built around this distribution
		- Under the null hypothesis [[#Null Hypothesis]] that there is no difference, we have the corresponding `t-distribution`, central at value of 0
		- If the difference is significant, the `t-value` will locate to two tails of the distribution

![[tutorial10.6.png]]

> Beyond Frequentist: Bayesian AB approach is becoming more and more popular. You can practice the Bayesian AB test in [exploratory.io](https://blog.exploratory.io/an-introduction-to-bayesian-a-b-testing-in-exploratory-cb5a7ad80963)

#### P-value
> Now, we come to the most important concept 
- T-test is one of the statistical test that could be applied for AB, we actually have many other tests applied for different scenarios, two most common tests:
	- `T-test` for mean comparison -> Continuous metrics
	- `Chi-Squared` for the comparoson of proportion metrics
- Regarless the test being applied, the results of them could be summarize as `p-val` 
- We will consider the `T-test`: When the `t-value` is larged, it's more likely that the Difference is different from zero. If it's larger than a threshold (depends on how confidence level we want to set), we will conclude that we reject the null hypothesis that there is no different at 1%, 5%, 10% confidence. Or in other words, we implied that there is a statictiscally significant difference.

![[tutorial10.7.png]]

- How `t-value` relates to `p-value`?
	- The relationship is illustrated by the graph above.
	- `P-value` (blue-shaded area) measure how "rare" (unlikely), we observe the `t-value` `>=` the observed value (calculated from the data by the formula above) - (See: [Statistics 101](https://www.youtube.com/watch?v=8YLvF5ztH90))
	
> By the graph, you can see: The larged `t-value` -> the smaller `p-value` -> The more unlikely we can observe those result under null hypothesis -> More confidence we should reject null hypothesis -> More statistically significant difference between 2 groups

#### Confidence Interval & Type I/II Error
> How we define the threshold of `t-value` and `p-val` to accept/reject Null hypothesis?

![[tutorial10.8.png]]

- Notice the tails of `t-distribution`, there are cases that we can still observe large `t-value` by chance (though it's rare it still can happen) -> Mistake of reject H0 when it's true: `Type I Error`
- The tail (blue-area) is the probability that the large `t-value` happened under null
	- This is also the probability that we reject H0, when it's true (still possible under t-distribution of null hypothesis)
	- More conservative, we will set a smaller `p-value` (smaller blue area), the `t-value` should be significant big to reject H0 (null hypothesis) -> We have stricter confidence level (>1%) toward `Type I error`
	- If less strict, we can have more relaxing confidence level (5%), we can reject H0 easier but riskier that `Type I error` might happen

## Analyze the result from AB Testing
- Besides the statistical testing, we can apply any simple analysis (Group By - Summarize) on the data or further EDA techniques in [[6-Lab-EDA-Viz]] to mine more insights from the data
- With data from AB Testing, we could also run some [[11-12-Regression-Analysis]] to explore the association between factors to the target metrics
- [[7-8-Segmentation-Analysis]] also helps you to deep-dive in the results -> You can run AB test on specific customer segment to have a better results

## Hypothesis - Distribution - Test Statistic 
> Above we learning that the p-val is the probability of the tails of the distributions of the test values (t-val)
- So that, `p-val` is connected to the Test statistics (for example: `-value` or `t-statistic`) and it's distributions

> How we determine which test to apply?

You could find many cheatsheets as below in the Internet:
![[attachments/tutorial10/tutorial10.9.png]]

How to actually understand it? Follow the logical flow as below:
1. `Test Statistic`: First we need a metric to measure the difference between 2 groups, depends on the hypothesis and the KPIs / outcomes, we might have different statistics metrics
	- Chi-squared: Categorical
	- t-test: mean of 2 sample
2. `Statistics Distribution`: the values of each `test statistics` in (1) will have the corresponding distribution

> Chi-square Distribution

![[attachments/tutorial10/tutorial10.10.png]]

> z-distribution & t-distribution

![[attachments/tutorial10/tutorial10.11.png]]

> Discussion: As in the above graph, t-test distribution will have fatter tails than z-test, comment about how it will impact the p-val, critical value to reject the null hypothesis at 5%

## Lab
[[10-Lab-ABTesting]]

## Exercise
[[10-Experiment-Design-Car-Service]]

## References
- [A/B Testing - by Google (Free Course)](https://www.udacity.com/course/ab-testing--ud257)
- [Intro to Inferential Statistics - by Facenook (Free Course)](https://www.udacity.com/course/intro-to-inferential-statistics--ud201)