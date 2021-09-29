> Dataset: [UCI Online Retail Dataset](https://www.kaggle.com/jihyeseo/online-retail-data-set-from-uci-ml-repo)
> Colab Notebook of this lab: [Online-Retail-RFM-Analysis.ipynb](https://colab.research.google.com/drive/1dCX_5FOtNE9GtJO3c4M0A4Lyl6FxIQ2y#scrollTo=jJbcHXkJj5QB)

## Data Explore
- This time we work with the Excel file (rather than the familiar `csv`) -> In pandas: `pd.read_excel()`
- Check the general info of the table
- Check the missing value -> `.dropna()`
- Check the statistics summary:
	- To show more quantiles than the default settings of the describe: `df1.describe([0.01,0.05,0.25,0.50, 0.75, 0.90,0.95,0.99])`
	- ==Notice that we have some outliers (you can also check by plotting the histogram)==
- Remove outliers:
	- Remove orders with `Quantity < 0` -> This is returned item
	- By the table reporting the percentile above (95%), we know the value to trim outliers (See: [[6-Lab-EDA-Viz]]) -> ==Though the cut-off not necessarily strict to the percentile value==

## Calculate R, F, M
> Create each element of RFM

1. `Recency`: 
	- data_latest_date: `df1["InvoiceDate"].max()`
	- recent_date of each customer: `df1.groupby("CustomerID").agg({"InvoiceDate":"max"})`
	- Take the gap in days between them
	- ==Check the distribution, treat the outliers if necessary==
		- Here rather than filter out, we replace value `>= p95` by the value of `p95`
2. `Frequency`: 
	- Count of unique date that a customer make transaction
	- Similar as above, check the distribution and treat the outliers
```
(df1.groupby("CustomerID")
	.agg({"InvoiceDate":"nunique"})
	.rename(columns={"InvoiceDate": "Frequency"}))
```

3. `Monetary`
	- Sum all value of orders

```
(df1.groupby("CustomerID")
	.agg({"order_value":"sum"})
	.rename(columns={"order_value":"Monetary"}))
```
## RFM-Score
1. Concat `Recency`, `Frequency`, `Monetary`
2. Bin each element to 5 bins (equivalent to 5-level scale in [[7-8-Segmentation]]) -> `R_Score`, `F_Score`, `M_Score`
3. Combine `R_Score`, `F_Score`, `M_Score` into `RFM_SCORE`

> Notice that: `RFM_SCORE` and element scores are categorical.

## RFM-based Segmentation
1. First, we check the distribution of Value (`Monetary`) by all `RFM_SCORE` we have by boxplot
	- Have up to 85 possible values of `RFM_SCORE` combinations of R, F, M 
	- This number of categories makes it not visible to obtain the insights -> Strategies to reduce the groups 

> Discussion: Why 85, but not 5 x 5 x 5 = 125?

2. Next, We can either: 
	- Group values in `RFM_SCORE` by business acumen (See: [[7-8-Segmentation]])
	- Clustering as we will practice in [[8-Lab-Kmeans]]
	- Below, we only use 2 elements R,F to segments and M as the aggregate metrics

```
seg_map = {
	r'[1-2][1-2]': 'Hibernating', ## Recency =< 2, Bad Frequency =< 2
	r'[1-2][3-4]': 'At Risk', ## Bad Recency =< 2, Mid Frequency from 3-4
	r'[1-2]5': 'Can\'t Loose', 
	r'3[1-2]': 'About to Sleep',
	r'33': 'Need Attention',
	r'[3-4][4-5]': 'Loyal Customers',
	r'41': 'Promising',
	r'51': 'New Customers',
	r'[4-5][2-3]': 'Potential Loyalists',
	r'5[4-5]': 'Champions'
}

 
rfm['Segment'] = rfm['R_Score'].astype(str) + rfm['F_Score'].astype(str)

rfm['Segment'] = rfm['Segment'].replace(seg_map, regex=True)
```

## Segment Analysis
### Boxplot by Segments
> Now we already construct the segments, we could do analysis (that we have learnt up to now GROUPBY-Summarize) with the RFM-based segments

- Boxplot of Value over RFM-based Segmentation
![[lab7.1.png]]

> Discussion: Comment on the graph above

- We can also analyze by each pair of 3 RFM elements
![[lab7.2.png]]

### Aggregates by Segments
- Calculate the aggregated metrics by Segments

```
rfmStats = rfm[["Segment","Recency","Frequency", "Monetary"]].groupby("Segment").agg(["mean","median","count", "std"])

rfmStats.columns = rfmStats.columns.map('_'.join).str.strip('|')
```

### Treemap
> This type of visualization, help us to understand the contributions of each segments on the whole volumes (or total value)

- We create the Segment Treemap by volumes (user_cnt): `rfm.Segment.value_counts()`
- Using the library `squarify`

```
df_plot = rfm.Segment.value_counts()
plt.figure(figsize=(10, 7))
squarify.plot(df_plot, label=df_plot.index, alpha=0.6)
plt.show()
```
![[lab7.3.png]]

> Distribution: Comment on the treemap

## Recap
- We practice how to generate the R, F, M in Python
- **R, F, M Score**:Use `pd.qcut()` to bin each element by percentiles, or `pd.cut()` if we want to bin by equal intervals 
- `RFM_SCORE`: combine the component scores
- Implement a segmentation by R,F in RFM model
- On the generated segments, we could do further analysis:
	- Boxplot: check the distribution of key metrics
	- Aggregate key metrics
	- Treemap: understand the contribution of segments
- Not only use to build the RFM, each of three components enable us to do further analysis on single, and pairs of RFM