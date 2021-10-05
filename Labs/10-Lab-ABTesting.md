> - Dataset: [cookie_cats.csv](https://www.kaggle.com/yufengsui/mobile-games-ab-testing)
> - Colab Notebook of this lab: [AB-Testing.ipynb](https://colab.research.google.com/drive/1swOr63N4jY8jWB1I-WelAbmLUHZrRBzv?usp=sharing)

## AB Test: Mobile Apps

![[attachments/lab10/lab10.2.png]]

> [Cookie Cats](https://www.facebook.com/cookiecatsgame) is a hugely popular mobile puzzle game developed by [Tactile Entertainment](http://tactile.dk). 
> It's a classic "connect three" style puzzle game where the player must connect tiles of the same color in order to clear the board and win the level. It also features singing cats. We're not kidding! As players progress through the game they will encounter gates that force them to wait some time before they can progress or make an in-app purchase. 
> In this project, we will analyze the result of an A/B test where the first gate in Cookie Cats was moved from level 30 to level 40. In particular, we will analyze the impact on player engagement through _Number of game rounds played by the player during the first 14d after install_. 

- Read about the dataset: [AB Cookie Cat Mobile Game](https://www.kaggle.com/yufengsui/mobile-games-ab-testing)
- **Target variable**: 
	- `sum_gamerounds`: The number of game rounds played by the player during the first 14 days after install
- **Treatment/Control groups**:
	- `version`: Whether the player was put in the control group (gate_30 - a gate at level 30) or the group with the moved gate (gate_40 - a gate at level 40)

## Data Explore
> You can conduct any EDA task on the dataset (See: [[6-Lab-EDA-Viz]]) 
- By `df.describe([0.01, 0.05, 0.10, 0.20, 0.80, 0.90, 0.95, 0.99]).T`, notice that the target variable `sum_gamerounds` has outliers
- We would need to remove the outlier. Here we apply the strategy to remove any data points larger than `p95` of `sum_gamerounds` 
```
df1 = df[df.sum_gamerounds < df.sum_gamerounds.quantile(0.95)]
```
### Naive ways to compare Group A/B
- `Groupby` the version, using aggregate functions for `sum_gamerounds`
- We see group `gate_40` have the mean slighly bigger
```
df1.groupby("version").sum_gamerounds.agg(["count", "median", "mean", "std", "max"])
```
### Distribution of `sum_gamerounds`
- As discussion in [[../Tutorials/10-AB-Testing]], `t-test` follows the assumption about normal distribution
![[attachments/tutorial10/tutorial10.11.png]]
- We will futhter check in [[#Normal Distribution]] (by statistical way), but here we try to plot and visually the distribution of `sum_gamerounds` are far from normal:
![[attachments/lab10/lab10.3.png]]
![[attachments/lab10/lab10.4.png]]

### Retention Summary
We also conduct some analysis on the retention of group A/B:
- `gate_30` seems to have 1% higher in `retention_1` and `retention_2`

## Target: `sum_gamerounds`
### AB T-test
> Run the AB test by `stats.ttest_ind()`

![[attachments/lab10/lab10.5.png]]

> Discussion: Interpret the result 

### Normal Distribution
> t-test is applicable under the assumption of Gaussian (Normal) Distribution of the target variable. We all see visually this assumption is violated in [[#Distribution of sum_gamerounds]]
- We also have the statistic test agaisnt the Null Hypothesis: ==H0: Distribution of y in sample X is normal==
```
normal_testA = shapiro(groupA)
```
- We test it by [**Shapiro test**](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.shapiro.html), similar to the high level ideas about `statistics test, p-val and hypothesis` in [[../Tutorials/10-AB-Testing]]
	- By identify the `H0`, knowing the `p-val`, we can have the conclusions

![[attachments/lab10/lab10.6.png]]

> Discussion: Interpret the result

### Mann-Whiteney Test
> Reference: [AB Testing](https://en.wikipedia.org/wiki/A/B_testing)
![[attachments/lab10/lab10.1.png]]
- In the case of non-normal distribution, we apply the [Mann-Whiteny test](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.mannwhitneyu.html?highlight=mann#scipy.stats.mannwhitneyu)
- The idea about `statistics test, p-val and hypothesis`, will stay the same (regardless statistics test and how we confuct the statistics-value)

![[attachments/lab10/lab10.7.png]]

> Discussion: Interpret the result
---

# OPTIONAL
## Target: `retention_1`
In the "naive" comparsion in [[#Retention Summary]], we observe a slightly higher `retention_1` for `gate_30` group.
> Let's try to run the AB test with the target is `retention_1`

- Import the data to `exploratory.io`
- Filter the outliers:
	- Add `New Step` -> `Create Calculation`
	- Create the `p95`
	- Add `New Step` -> Remove points with `sum_gamerounds` < mean (or max, min) of `quantile_sum_gamerounds` (why: because by construction, this is a constant, only get the value of 221)
	
![[attachments/lab10/lab10.9.png]]
![[attachments/lab10/lab10.10.png]]

- How the dataset looks like:
![[attachments/lab10/lab10.12.png]]

### Chi-squared: Proportion
As `rentetion_1` is the proportional (YES/NO), we apply the **Chi-square**:
![[attachments/lab10/lab10.17.png]]
> Discussion: Interpret about the result

### Challenges of Frequentist
> To know the frequency, we need to count, and have a large enough sample to count.

1.  We need to know how much of the data we need to collect for the test before starting the test.
2.  Because it's sensitive to the sample size, We can’t evaluate the result in real-time as we go, instead we need to wait until we collect a full of the planned data size.
3.  The test result is not intuitively understandable especially for those without a statistical background. It's difficult to explain the p-val to business stakeholders
4.  The test result is interpreted as black and white, either it is statistically significant or not. This makes it hard to figure out what to do especially when **not statistically significant**.

Because of that, it's desirable to chose Bayesian approach.

### AB Test - Bayesian
As you can see below, Bayesian A/B tesint give you the result as the `chance that A is better (or worse than B)` -> Very intuitive to understand and communicate:

![[attachments/lab10/lab10.13.png]]


- Behind Bayeisn:
	1. `Prior Distribuion`: is the starting distribution of `retention_1`, it's assumed the uniform distribution for group A and B
	2. With any addition data point, the `prior` is updated, until we have the `posteriors` of Group A and B
	3. With the `posteriors` of Group A and B, we can compute the improvement rate `(A-B)/B` and have the distribution for `Improvement Rate`

![[attachments/lab10/lab10.15.png]]
![[attachments/lab10/lab10.16.png]]
![[attachments/lab10/lab10.14.png]]
- By that, the power of Bayesian is that it enables us to go beyond Reject/Fail to Reject (black-and-white) answer, the distribution of `Improvement Rate` helps us to know:
	- `Chance of Being Better`: blue area
	- `Expected Improvement Rate` (How much A is better than B)
- Though Frequentist is still very popular, as Bayesian method is computation costly (it's a serious problem with big datasets)

## Recap
- We learn the AB `t-test` for the mean of `sum_gamerounds` to compare 2 settings in the mobile apps
- Visually and statistically, we know that `sum_gamerounds` is not normal distribution that we apply the **Mann-Whiteney Test**
- Move to `exploratory.io`:
	- If `retention_1` is the target, it is proportion metrics, we apply the `Chi-squared` for it
	- We also introduce the concept of Bayesian AB testing