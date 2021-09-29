> Dataset: [UCI Online Retail Dataset](https://www.kaggle.com/jihyeseo/online-retail-data-set-from-uci-ml-repo)
> Colab Notebook of this lab: []()

## Data Explore
This is similar to [[7-Lab-RFM]]

## Clustering Data Preparation
> Some further steps to get the data ready for the K-means clustering.

### Customer-level Metrics
The R, F, M in [[7-Lab-RFM]] is user-level metrics (Groupby: `CustomerID`).
In Clustering, we also need to generate some user-level metrics.

> Discussion: Recommend some user-level metrics that meaningful for us to use for the segmentation.

In this exercise, we generate:
- `Total of Customer Value`
- `Number of Orders (Frequency)``
- `Number of Unique Products`
- `Average Value per Order`

> Discussion: Do we have any hypothesis about these three metrics?

### Normalize
- By `.describe()`, we see that these three metrics have very different scales:
	- `Total of Customer Value`: could be up to thousands
	- `Order Count`: rarely above few hundreds
	- `Unique Products`: rarely up to 100
	- `Avg. Value per Order`: much smaller than total value
- **K-means clustering** is based on distance (to determine the similarity), so this algorithms is sensitive to scales of metrics
- ==We need to normalize so they have the same scale.==

There are many different way to do, but we will apply the two-step approach:

1.  Rank metrics so each customer get the rank value from 1 to N = 4221 (total customers) => This is to remove any possible outliers

```
# Step 1: Rank
rank_df = customer_df.rank(method='first')
rank_df.head()
```
2.  Normalize it by center around mean, and normalize by standard deviation -> Each series will have mean of 0 and std = 1

```
# Step 2: Normalize
normalized_df = (rank_df - rank_df.mean()) / rank_df.std()
normalized_df.head()
```

## K-means Clustering
- Choose the `k=4`, run the k-means clustering:
```
kmeans = KMeans(n_clusters=4).fit(normalized_df[['TotalValue', 'OrderCnt', 'UniqueProduct', 'Avg_OrderValue']])
```
- Assign the **Cluster** `kmeans.labels_` to our customer-level dataframe:

```
four_cluster_df = normalized_df[['TotalValue', 'OrderCnt', 'UniqueProduct', 'Avg_OrderValue']].copy(deep=True)
four_cluster_df['Cluster'] = kmeans.labels_
```

## Analyze Segments
### Scatter Plots
> By each pair of factors (for 2-D plot), using the Scatter plots will help us to have some insights.

![[attachments/lab8/lab8.2.png]]

![[attachments/lab8/lab8.3.png]]

![[attachments/lab8/lab8.4.png]]

> Discussion: Comment about the business meanings of any insight above.

### Other Segmentation Analysis
> Similar to what we do in [[7-Lab-RFM]] Segmentation, we can also do it with the Clusters

![[attachments/lab8/lab8.5.png]]

### What's each group buy?
> This task could also do for the Segments in [[7-Lab-RFM]].

1. Merge the customer-level clusters DF to the original dataset at customer-order level
```
merged_df1 = pd.merge(df1, four_cluster_df['Cluster'].reset_index())
merged_df1.head()
```
![[attachments/lab8/lab8.6.png]]
2. For group high-value and low-value, Summarize to `product-level` (Groupby: `Description`):
	- Count number of invoice
	- Unit price

3. Insights:
	- For high-value users: `T-Light Holder`, `Jumb/Lunch Bag Retropost`,`Lunch Bag Retro` are best seller -> By unit price, they are not significantly expensive item, but the quantity per order is high
	- For low-value users: `T-light Holder`,`Rex Cash`, `Cakestand`. `Postage` are best seller -> By unit price, they are more or less same, but the quantity per order is low
		- Especially, we have the Postage with high-price, but low-quantity 

> Discussion: What do you want to do further analysis to obtain more insights.

## Recap
- Clusters and Segments are very similar, though we apply different techniques, the goals and possible ways to analyze the results are quite similar
- For Clustering, we would need to:
	- Generate customer-level metrics
	- Clustering algorithms are sensitive to scale, that we would need to normalize the scale, and handle outliers
- After the clustering, by scatter plots (with Clusters), we could obtain insights about the behavioral patterns of customers
- **Elbow graph**, the data-centric way to choose the number of `k` is not in the scope of this lab. But you can check on this [Notebook](https://www.kaggle.com/maianhdang/all-in-one-notebook-common-ml-algorithms#%F0%9F%93%8A-EDA:-Continuous). In reality, I find people rely on business acumen to choose the `k` more often (as you also see in the cited notebook, at the end, the exercise follows some human-sense number of cluster, rather than the suggestion of data-centric methods). Commonly, people choose the `k` between 3-5 clusters