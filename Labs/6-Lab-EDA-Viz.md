> Colab Notebook of this lab: [EDA-Viz.ipynb](https://colab.research.google.com/drive/1OL1SZXPb9k_vd9ZBiBEs25PJrUNhm3c5?usp=sharing)
> Dataset: [Ecommerce Customers](https://www.kaggle.com/srolka/ecommerce-customers?select=Ecommerce+Customers.csv)

> *Discussion*: Download and understand the data (via `Data Explorer` feature of Kaggle)
> *Hints*: Remember what to notice about the structure of data in [[1-2-DWH & SQL]]

## EDA
![[lab6.1.png]]

`EDA` is the steps to get the understandings about the data to suggest:
- Check quality of data for further processing and cleaning (solve missing value, combine columns, etc.)?
- Formulate assumptions and hypothesis (beside from business-sense hypothesis) - such as the distribution of data

## Colab Notebook
- Upload the data file to Colab (See: [[2-BigQuery-Colab]])
- Import the library: 
	- `numpy`: for numeric computation
	- `pandas`: for table (DataFrame) manipulation
	- `matplotlib.pyplot`: for visualization
	- `seaborn`: for visualization
	- `missingno`: to check the missing value
- Import `CSV` through `pandas`
```
df = pd.read_csv('train.csv', low_memory=False)
```
## SQL vs. Python
| Task                 | SQL                                                         | Python                                       |
|----------------------|-------------------------------------------------------------|----------------------------------------------|
| Preview Data         | SELECT * FROM df LIMIT 5                                    | `df.head()`                                  |
| Columns Subsets      | SELECT col1, col2 FROM df                                   | `df[['col1','col2']]`                        |
| Columns Manipulation | SELECT col1 + col2 AS col12 <br> FROM df                    | `df['col12'] = df.col1 + df.col2`            |
| Filtering            | SELECT * FROM df <br> WHERE col1 > 0                        | `df[df.col1 > 0]`                            |
| Aggregate/Group By   | SELECT col1, col2, AVG(col3) <br> FROM df <br> GROUPBY 1, 2 | `df.groupby(['col1','col2'])['col3'].mean()` |
| Change DataType      | CAST()                                                      | `.astype()`                                  |
| DISTINCT             | SELECT DISTINCT col1 FROM df                                | `df.col1.unique()`                           |
| COUNT DISTINCT       | SELECT COUNT(DISTINCT col1) FROM df                         | `df.col1.nunique()`                          |

## 1 - Preview Data
1. Preview Data: `df.head()`
2. Check columns: `df.columns`
3. Check total number of entries and column types, null-values: `df.info()`
	- **Processing** (if necessary ): add more columns
5. Check granularity: `df['email'].duplicated().sum()`
6. Check Duplicates: `df[df.duplicated()]`
	- **Processing** (if necessary): `df.drop_duplicates(inplace=True)`
7. Check missing value: 
	- `df[df.isnull().any(axis=1)]`
	- `missingno.matrix(df)`
	- **Processing** (if necessary):
		- Remove: `df.dropna()` (if the missing value is small, set `thresh` - threshold)
		- Replace: [Medium - Working with Missing Values in Pandas](https://towardsdatascience.com/working-with-missing-values-in-pandas-5da45d16e74)
			- `df['col1'].fillna(value=df.col1.median())`
			- `df.fillna(axis=0, method='ffill')`

> Discusstion:
> 1. What is the most important variables to the business?
> 2. What information we have in the table? (Columns)
> 3. What is the granularity of the table?
			
## 2 - Data Distribution
1. Statistics Summary: `df.describe()` (numeric, categorical, all)
2. Numeric Distribution:  `sns.pairplot()`
3. Categorical Barplot Distribution
```
(df
.groupby('email_domain').yearly_amount_spent.median()
.sort_values(ascending=False).head(70)
.plot.bar(figsize=(15, 4)))
```
![[lab6.2.png]]

> Discussion:
> 1. Comment about the distribution
> 2. By Scatterplot in `sns.pairplot()`, do we observe any relationship between 2 variables?

## 4 - Univariate Analysis
### Histogram
```
(df['yearly_amount_spent']
.plot(kind='hist', bins=50, figsize=(12,6), facecolor='grey',edgecolor='black'))
plt.xlabel('yearly_amount_spent')
plt.title('Histogram of yearly_amount_spent')
```
![[lab6.3.png]]

> Discussion:
> 1. Comment about the tendency of data: central value (mean, median), mode (most frequent value)
> 2. Comment about the spread of data?
> 3. Comment about how the data "skew"

![[lab6.4.png]]

### Boxplot
> Simplified version of histogram
![[lab6.5.png]]

```
df.boxplot('yearly_amount_spent', vert=False)
plt.title('Boxplot of yearly_amount_spent')
```
![[lab6.6.png]]

### Preprocessing: Remove Outliers

## 5 - Multivariate Analysis
### Distribution by Groups (Numeric x Categorial)
> Similar to the idea of Pivot (See: [[4-Lab-Metrics]])
- Compare the distribution of a numeric variables (`yearly_amount_spent`) by different categories (`state`)
```
sns.boxplot(x='state', y='yearly_amount_spent', data=df.sort_values(by='yearly_amount_spent',ascending=False))
```
![[lab6.7.png]]

> Discussion: Comment about the insights in the distribution of `yearly_amount_spent` among different `state`?

### Joint Plot (Numeric x Numeric)
> There are many variants of Seaborn Join plots (Check: [Seaborn Docs](https://seaborn.pydata.org/generated/seaborn.jointplot.html))

- Scatteplot could be used to visualize the association between 2 numeric variables
- We can choose the option `kind='reg'` to visualize the linear relationship between 2 numeric variables (Will learn in [[11-12-Regression-Analysis]])
- On the marginal of x-axis, and y-axis, we have the (marginal distribution) of each variables

```
sns.jointplot(data=df, x="yearly_amount_spent", y="length_of_membership", kind="reg")
```
![[lab6.8.png]]

### Correlation Heatmap (Numeric x Numeric)
- Correlation get value from `-1` to `1`
	- `-1`: *Negative Correlation* (2 variables move with opposite direction)
	- `1`: *Positive Correlation* (2 variables move with same direction)
	- Close to `0`: No relationship
- Correlation does not mean Causality
![[lab6.9.png]]

> Discussion: Comment about the relationship among variables from the Correlation Heatmap (Compare with Pairplot in [[#2 - Data Distribution]])

## Further Data Processing
### Convert Numeric to Categorical
- **Numeric to Categorical**: Combining a range of values into bins, as in [[5-Lab-Pirate-Metrics]] (`age`)
- Different ways to cut the numerics into bins:
	- Arbitrary
	- Equal intervals
	- Quantiles (so the number of observation is each bins is equal)
- Bin `length_of_membership` into 3 equal-size bin: `pd.qcut(df.length_of_membership, 3, labels=['low','mid','high'])`
- This allows us to analyze some clearer patterns in Data
![[lab6.10.png]]

### Convert Categorical to Numeric
- **Categorical to Numeric**: Into 0/1 Binary as we learn in [[5-Lab-Pirate-Metrics]] (`conversion`)
	- One-hot code: `pd.get_dummies(df[cat_cols])`
	- Check: `one_hot_df.info()`

```
Data columns (total 3 columns): 
# Column Non-Null Count Dtype 
--- ------ -------------- ----- 
0 avatar_theme3_Blue 500 non-null uint8 
1 avatar_theme3_Green 500 non-null uint8 
2 avatar_theme3_Others 500 non-null uint8
```
### Detect/Remove Outliers
- How to define Outlier
	- For Normal Distribution:
		- Data points which fall below `mean-3*(std)` or above `mean+3*(sigma)` are outliers
		![[lab6.11.png]]
	- For Skewed Distribution
		- `Inter-Quartile Range (IQR)`: `IQR = Q3 - Q1`, Q1 and Q3 is 25th and 75th percentile
		- Data points which fall below `Q1 – 1.5 IQR` or above `Q3 + 1.5 IQR`
	- Other percentile-based approach: Data points that are far from 99% percentile and less than 1 percentile are considered an outlier

![[lab6.12.png]]

## Recap
1. We go step-by-step through a standard process of EDA
	- General review: columns, rows, missing, duplicates
	- For univariate: We care about the distribution (central tendency and how data spread), this could be interpreted via:
		- Statistics summary
		- Boxplot
		- Histogram
	- For Multivaraite: We care how different variables associates with each other (especially the target variable of the case)
		- Join Plot
		- Boxplot by Groups
		- Correlation Heatmap
2. Data Cleaning will happen along the way with EDA (add columns, transform data, solve missing values, remove duplicates)
	- Replace/Remove missing value
	- Remove/Replace outliers
	- Remove duplicates (De-duplicates)
	- Create/Transform columns
3. Categorical and Numeric could be converted to each others
4. Detect and treat outliers by IQR

## References
1. [EDA from Scratch in Python](https://medium.com/swlh/exploratory-data-analysis-eda-from-scratch-in-python-8c12c2673aa7
2. [Useful Seaborn Plots for EDA](https://simply-python.com/2019/08/21/useful-seaborn-plots-for-data-exploration/)
3. [How to detect and remove outliers with Python](https://www.analyticsvidhya.com/blog/2021/05/feature-engineering-how-to-detect-and-remove-outliers-with-python-code/)