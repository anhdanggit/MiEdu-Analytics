> In this lab, we will demonstrate how to compute and visualize the KPIs in Marketing
> - Dataset: [UCI's Bank Marketing Data set](https://archive.ics.uci.edu/ml/datasets/bank+marketing) -> Click `Data Folder` > `bank-additional.zip` -> Use `bank-additional-full.csv`
> - Tools: BigQuery SQL to compute the KPIs and Data Studio ot visualize

- In the tutorial [[4-Metrics Design]], we know that KPIs are popular for any business. They are the hearts of any reporting, dashboarding and BI system. 
- Every industry (gaming, finance, payment, ecommerce) has a set of default metrics. So that once we enter a new DA position in new market, researching about those common KPIs could be the very first thing we should do.
- Each operations or process of the company could be decomposed into a set of key metrics to measure the performance


## 1. Marketing KPIs
> As the name, KPI (`Key Performance Index`) is the metrics designed to measure the performanced
- `ROI (Return on investment)` 
	- Every marketing effort costs money to the company (e.g. when you run marketing cmapaigns through emails, sending each email costs some money) -> How the measure `how good we spend money?` 
	- ==How many dollars we earn back, given 1 dollar we spend for marketing?==
- `Sale Revenue`
	- Goal of marketing is to generate and grow more revenue
	- To identify the effectiveness of marketing efforts to drive revenue -> ==Need to attribute the revenue to marketing efforts==
	- In realuty, this is tricky tasks (remove the seasonal, trend without any marketing efforts, etc.)
- `CPA (Cost per Acquisition)`
	- How expensive the marketing campain?
	- How much is your cost to acquire a customers through marketing efforts? -> Highed CPA means it costs more to acquire new customers

### Discussions
1. If we could only have 1 metric to compare the performance of marketing effort, what it should be?
2. Why do we need all above metrics to measure the marketing performance?
3. In what cases, `CPA` will be high? (*Hint*: business models, industry, etc.?)
4. In what cases, `CPA` is high but the business is still profitable?
5. In what cases, `ROI` is high, but `Sale Revenue` is not high? (*Hints*: `ROI` is ratio, `Sale Revenue` is absolute value)
6. In your opinion, how to optimize a Marketing efforts, considering 3 metics: `ROI`, `CPA`, `Sale Revenue`

## 3. Break-down 
> Now, we break down the marketing results (`ROI`, `CPA`, `Sale Revenues`, `Revenue (Sales) per Customer`) by `campaigns` (each campain is an effort)

![[lab4.1.png]]

### Discussions
1. What is the most expensive campaign?
2. What is the most valuable campaign (generated the most revenue)?
3. What is the campaign that brings most customers?
4. How the `Value of Campaign` defined?
5. Compute `ROI`?
6. What campaign is problematic and need to take actions to improve?


## 4. Digital Marketing KPIs
- Metrics in section [[#1 Marketing KPIs]] is common for traditional marketing (still applicable for online marketing).
- Once the marketing channels grow into online space (Facebook, Ads on Mobile Apps, Search Engine, etc.). The online settings enable us to track more touch points of customer, hence we have more digital marketing KPIs as following the funnel:
	- **Concept of 'Funnel'**: Input as 100% (numbers of peoples), drops after each step (lower inputs)
		- `Number of downstream` / `Number of upstream` (<100%)

![[lab4.2.png]]

> Steps of Funnel:
> - (1) Ad Views (Impressions) -> (2) Ad Clicks -> (3) Qualified Leads -> (4) Expected Outcomes (Sign-up, Purchase, Subscription, etc.)

> Metrics:
> - CTR (2/1)-> Lead Ratio (3/2) -> Conversion Rate (4/3)

### CTR
`Click-through Rate (CTR)`: Measure how effective your online marketing brings traffic to website -> % of people ưho viewed the advertising and went on to click on the advertising.

> `Click-through Rate (CTR)` = `Number of Clicks` / `Number of Views`

![[lab4.3.png]]

### Lead ratio 
`Lead Ratio`: Measure how much of trafic to website is a good fit to become a customer (*qualified leads*: ready to be marketed and meet business-specific criteria to become customer who are likely to make purchases -> [`Lead Scoring`](https://en.wikipedia.org/wiki/Lead_scoring)

> `Lead Ratio` = `Number of Qualified Leads` / `Number of Clicks`

### Conversion Rate
`Conversion Rate`: Measure the % of leads that are converted into a desired customers, depends on marketing goals.
- Could apply to different desired actions: 
	- `Number of paying customers` /  `Number of Leads`
	- `Number of payng customer > $xxx` / `Number of Leads`
	- `Number of Sign-up` / `Number of Leads`

## 5. CSV to BigQuery
- Download dataset: [UCI's Bank Marketing Data set](https://archive.ics.uci.edu/ml/datasets/bank+marketing) -> Click `Data Folder` > `bank-additional.zip` -> Use `bank-additional-full.csv`
- Read the definition of each column: `bank-additional-names.txt`
- Open the csv (by Excel to check the contents)

#### Create Dataset
- Open GCP (Google Cloud Platform) -> BigQuery
- Under your project, `Create dataset`
![[lab4.4.png]]
- Naming the dataset (as this is only for tutorial, you can set the time to auto-delete the table after xx days)
![[lab4.5.png]]

#### Create Table
- Open the newly created dataset -> `Create Table`
	- `Source`: Choose `Upload` -> `Browse` and direct to where you store the CSV on your machine
	- `Destination`: Info about the datasets/project you want to host the data

![[lab4.7.png]]

#### Set Schema
By input in UI, for `Edit as Text` as below: 
```
age:NUMERIC,job:STRING,marital:STRING,
education:STRING,default:STRING,
housing:STRING,loan:STRING,contact:STRING,
month:STRING,day_of_week:STRING,duration:NUMERIC,
campaign:NUMERIC,pdays:NUMERIC,previous:NUMERIC,poutcome:STRING,
emp_var_rate:NUMERIC,cons_price_idx:NUMERIC,cons_conf_idx:NUMERIC,
euribor3m:NUMERIC,nr_employed:NUMERIC,y:STRING
```
![[lab4.8.png]]

#### Advanced Options
- Field delimiter: `;`
- Header row to skip: `1`

![[lab4.9.png]]

#### Preview BigQuery Table
Check the table in BigQuery
![[lab4.10.png]]

## 6. Compute & Visual KPIs
### Conversion Target
- `y`: which has information on whether a client has subscribed to a term deposit, is encoded as 'yes' or 'no'
- For convenience, generate the column `conversion` = 1 if `y = 'yes'`, and `= 0` otherwise (convert from categorical/string to numeric)
- Replace your project path after the `FROM` 
```
SELECT
	*,
	IF(y = 'no', 0, 1) AS conversion
FROM `analytics-tutorial-326206.bank_marketing.bank_additional`
LIMIT 10 -- limit 10 rows to test first, remove it in final output
```
### Aggregate conversion rate
> For the whole dataset, the conversion rate is `11.27%`
```
WITH tmp AS (
	SELECT
		*,
		IF(y = 'no', 0, 1) AS conversion
	FROM `analytics-tutorial-326206.bank_marketing.bank_additional`)

SELECT
	SUM(conversion) AS conv_sum, -- instead of COUNTIF(y='yes')
	COUNT(*) AS client_num,
	SUM(conversion)/COUNT(*) AS conversion_rate1,
	AVG(conversion) AS conversion_rate2 -- why conversion is convenient
FROM tmp
```

### Conversion Rates by Age
> Now, compare the conversion rate among age groups

```
WITH tmp AS (
	SELECT
		*,
		IF(y = 'no', 0, 1) AS conversion
	FROM `analytics-tutorial-326206.bank_marketing.bank_additional`)

SELECT
	age,
	SUM(conversion) AS conv_sum, -- instead of COUNTIF(y='yes')
	COUNT(*) AS client_num,
	SUM(conversion)/COUNT(*) AS conversion_rate1,
	AVG(conversion) AS conversion_rate2 -- why conversion is convenient
FROM tmp
GROUP BY 1
ORDER BY 1
```
![[lab4.11.png]]

> Visualize for better view -> `Explore Data`

This will direct you to Data Studio (free)
- On chart type: Select line chart
![[lab4.12.png]]

- On left tab:
	- Dimension: `age`
	- Metrics: `AVG: conversion_rate`
	- Sort: `age` (sorting the values on x-axis)

![[lab4.13.png]]

> Discussion: Explain the trend.

Notice that:
1. We have only small number of client on age >90+, that make the rate metrics is not reliable (this is also the shortcomings of rate metrics) -> **Noise**
2. How to sovle the *Unwanted noise*, group multiple ages together, so we have more number of client per groups.

```
WITH tmp AS (
	SELECT
		*,
		IF(y = 'no', 0, 1) AS conversion,
		CASE
			WHEN age < 30 THEN '18-30'
			WHEN age < 40 THEN '30-40'
			WHEN age < 50 THEN '40-50'
			WHEN age < 60 THEN '50-60'
			WHEN age < 70 THEN '60-70'
			ELSE '70+'
		END AS age_group
	FROM `analytics-tutorial-326206.bank_marketing.bank_additional`) 

SELECT
	age_group,
	SUM(conversion) AS conv_sum, 
	COUNT(*) AS client_num,
	AVG(conversion) AS conversion_rate 
FROM tmp
GROUP BY 1
ORDER BY 1
```
![[lab4.14.png]]

> Discussion: Comment any insights from the chart.

### Pivot table: Conversions vs. Non-Conversions
To identify the difference between converted group and non-converted group, we can use the pivot table:

![[lab4.15.png]]

> How to conduct it in SQL?
```
SELECT
	y,
	marital,
	COUNT(*) AS client_num
FROM `analytics-tutorial-326206.bank_marketing.bank_additional`
GROUP BY 1, 2
```
> Data Studio: Select `Pivot Tables` (or pivot tables with bars)
- Row Dimension: `marital`
- Column Dimension: `y`
- Metrics: `SUM(client_num)`
- Sorting: `client_num` (Descending)

![[lab4.16.png]]

### Conversion by Age x Marital Status
```
SELECT
	education,
	marital,
	COUNTIF(y='yes') AS conversion_cnt
FROM `analytics-tutorial-326206.bank_marketing.bank_additional`
GROUP BY 1, 2
```
> Data Studio: Select `Column Chart`
- Dimension: `education`
- Breakdown Dimension: `marital`
- Metrics: `SUM(conversion_cnt)`

![[lab4.17.png]]

## Recap
1. We learn some key metrics in marketings, and explain the concept of funnel and conversion rate
2. Practice with the Bank Marketing dataset on BigQuery and Data Studio
3. Break-down to dimensions to compare the KPIs among different groups
4. Compare the demographies of conversion and non-conversions (Age x Marital) by pivot tables