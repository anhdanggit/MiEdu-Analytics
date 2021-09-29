> Dataset:
> - [sample_cohort.csv](https://exploratory.io/tag/?q=tag:%22Sample%20Data%22)
> - [UCI Online Retail Dataset](https://www.kaggle.com/jihyeseo/online-retail-data-set-from-uci-ml-repo)
>
> Colab Notebook of this lab: [Online-Retail-Cohort-Analysis.ipynb](https://colab.research.google.com/drive/1Mb11FG3I_l1HVWoue_feeJx1jRQNGaMt?usp=sharing)

Improve the product and service's retention rate (and decrease churn rate) is one of the most important of any business. 

Retention or Churn Rate is one of the key engines in many analytics framework we have learnt (See: [[5-Analytics Frameworks]])

We will:
- Using `exploratory.io` to:
	- Construct the survival curve
	- Run the cohort analysis
- In Python, we also introduce the cohort analysis:
	- Total Sales
	- Monthly Active Users
	- Retention Rate

## Check the Dataset
> Load the dataset to `exploratory.io`

![[lab9.1.png]]

> Discussion: Have a look on data and comment 
> 1. Granularity and Information?
> 2. Which business, this data come from?
> 3. For the purpose of retention (churn) understanding, which information are interesting for us?

## Survival Analysis
In `Analytics` tab, set
- Type: `Survival Curve`
- Start time: `start_time`
- Date time: `end_time`
- Period By: `Day`
- Event Status: `canceled`

> Here our survival curve is generated.

![[lab9.2.png]]

- `X-axis`: how many periods (`Day`) passed from the `start_time`
- `y-axis`: survival rate
	- `100%` in Day0
	- Decline over time (very few customers still active after 400 days)

![[lab9.3.png]]

## Interpret
- The curve will tell us how fast the customers will "cancel away" from us, day-by-day after Day0:
	- In the chart above, at Day30, we only have `61%` of users still being with us
- The steeper slope of the curve, means that customers churn very quickly from us
- When a line is below another line, it has lower survival rate with the above, at the same period

![[lab9.4.png]]

- `Group-by` `OS`: set the `Color By = 'os'`. We have the graph as below:

![[lab9.5.png]]

> Discussion: From the chart, comments about the pattern of retention (survival rate)

- At the same period, let's day `D30`:
	- Survival Rate of Mac (*'Orange'*): `63%`
	- Survival Rate of Windows (*'Green'*): `60%`
	- Survival Rate of Linux (*'Blue'*): `48%`
- By the curve, we see that:
	- Linux customer churns very quickly in first 30-40d, when who still stays after that is very loyal (`hard-core fan`) and stay with us
	- Another thing we needs to be cautious is that the number of observation in Linux (when we hover the mouse, it will displayed) is very low -> ==So, the result here might not be representative== -> We might even should filter out Linux from our analysis
	- For the other two OS, their customer churns gradually over time, with Windows customers churn faster than OS.

> Discussion: What business questions we can have here? How the above insights might be useful for the business?

## Cohort Analysis
The techniques to give us insights about retention and churn is crucial, and the best way to monitor and analyze is `Cohort Analysis`.

It helps us to understand how retention (churn rate) improve or decrease by groups of users joining at different times, and by other characteristics: different countries, operationing system (os), different groups of behaviors.

> We want to split this survival curve into multiple groups -> `Color By` -> Choose the Frequency

1. Joined Month
2. Joined Quarterly
3. Joined Year

![[lab9.6.png]]

> Discussion: Comment on the results

- We can split the survival curve by different cohorts and split it further by OS

![[lab9.7.png]]

> Discussion: Comment on the results

## Retention Analysis
> Follow: [Online-Retail-Cohort-Analysis.ipynb](https://colab.research.google.com/drive/1Mb11FG3I_l1HVWoue_feeJx1jRQNGaMt?usp=sharing)

- `Online Retail.xlsx` is a familiar dataset that we used in [[7-Lab-RFM]] and [[8-Lab-Kmeans]] that we will conduct the data exploration and steps of cleaning data similar in last labs. 
- In this lab, we focus on this subset:

```
df[(df.Quantity <= 100) & (df.order_value <= 100) & (df.order_value > 0)]
```
### Prepare the Data for Cohort Analysis

#### Step 1: `invoice_period`
Generate the `invoice_period` by extracting Year-Month of `InvoiceDate`
- `df['InvoiceDate'].apply(lambda x: x.strftime('%Y-%m'))`
- By this, we change the frequency from Daily to Monthly (similar effect as the feature `Period By` in `exploratory.io` [[#Cohort Analysis]])

#### Step 2: `cohort_group`
Generate the `cohort_group`, which is the `%Y-%m` of `start_date` (min of `InvoiceDate`) of each customer
- Set index to `CustomerID`
- `groupby(level=0)` which is equivalent to groupby `CustomerID`
- Take the `start_date`: `.min().apply(lambda x: x.strftime('%Y-%m'))`
- Remember the reset the index

> Discussion: What happend if we do not set the index and `groupby(level=0)`? Hint: Try to run `groupby('CustomerID')` directly?

#### Step 3: Aggregate Metrics
Group by:
- `cohort_group` from [[#Step 2 cohort_group]]
- `invoice_period` from [[#Step 1 invoice_period]]
- Aggregate:
	- Count number of distinct customer
	- Count distinct product
	- Sum the Quantity
	- Sum the value of orders

The results as follows:
![[lab9.8.png]]

- For each `cohort_group`: groups of users have the start-date in same month `%Y-%m`
	- Track the aggregated metrics, for each period in their life cycle (`invoice_period`)	

#### Step 4: `cohort_period`
Remember the charts in [[#Cohort Analysis]] in `exploratory.io`, the period is integer 1, 2, 3, for 1-month, 2-month, 3-month after the start-date

- We want to do something similar in Python
	- For example: `cohort_group = '2010-12'`
		- `invoice_period = '2010-12'`: period = 1
		- `invoice_period = '2011-01'`: period = 2
		- So on...

> Here we could start counting by either 0 or 1, depends on how it's easy to interpret for stakeholders (some are not familiar counting things from zero)

- Create the function: `cohort_period(df)`
- Combine it with `.apply()`, so it will apply the function for each row of the dataframe
- The result as follows"
![[lab9.10.png]]

### Cohorts table: From long-form to wide-form
![[lab9.13.png]]

Now, we want to pivot the table, so we will spread `cohort_period` to columns, and `cohort_group` in rows:

- Set index to these two columns: `cohorts.set_index(['cohort_period','cohort_group'], inplace=True)`
![[lab9.11.png]]
- Pivot the table (==from long-form to wide-form==)

```
cohorts['total_value'].unstack(0)
```

### Create Heatmap
#### Monthly Total Sales
Using the heatmap to create the table of distribution of sales across cohorts during their life-cycle (`cohort_periods`):

![[lab9.12.png]]

> Discussion: Comment on the heatmap 

#### Retention
> Retention of each cohort over time (periods) is the number of active users at each period, divide for the cohort size (= user count in first periods)
- Size of cohort (`denomiator`): `cohorts.groupby(level=1)['cust_cnt'].first()`
- Spread the `cust_cnt` - active users over periods of each cohorts (instead of `order_value`): `cohorts['cust_cnt'].unstack(0)`
- Divide for the **retention rate**: `cust_retention.divide(cohorts_size, axis=0)`

> Discussion: Comment on the heatmap 

![[lab9.14.png]]

## Recap
- Construct the survival curve, which is the rate of users still using the product/services after periods of time from the start-date
- **Cohort Analysis**: For deeper insights, we split the curve for different cohort
- Implement Cohort Analysis with Python on `Online Retail.xlsx`
	- Combine using `.set_index()` and `.reset_index()` to manipuate DataFrame with Pandas
	- Visualize the Cohort Analysis by Heatmap matrix (in `Seabonr`)
