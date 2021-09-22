Segmentation is the technique to uncover information, by ==Categorizing and understandings==. By segmenting customer, we can compare and tailor the treatment for each segment (for example: different pricing, promotions, product placement)

## Customer Segmentation
> The general principal of segmentation is to split the population into subgroups by various customer attributes, so that (we hope) the members within a subgroup share something common, and between subgroups we hope to see some differences in patterns. 

> *For example*: 
> Split by age, so member in young ages are expected to behave similarly. While the behaviors of young ages are different from the group of middle age.
- When segmenting the customer base into subgroups, we can use metrics that represent characteristics and their statistics to describe and compare among groups
- Segmentation could start with some arbitrary subgroups: demographic info (age, gender), geographic infor (state, post code), behaviors (transactions). For example:
	- In [[6-Lab-EDA-Viz]]:
		- Segment by States as in  - Distribution of `yearly_amount_spent` by states
		- Convert numerical variable `length_of_membership` to high-mid-low bin and compare the `yearly_amount_spent`
	- In [[4-Lab-Metrics]]:
		- Segment clients by `age_group` and compare `conversion_rate`
		- Segment clients by `marital` and compare `conversions`
		- Segment clients by both `education` and `marital` to compare `conversions`
- It could become exponentially difficult, when the number of attributes to segment users increases (that we can use some Machine Learning for the task of customer segmentation)

## Objectives
Segmentation can help a business in multitude of ways:
- `Customer Understanding`: deeper understandings about customers, their attributes and behaviors
- `Target Marketing`: enable the business to focus marketing effort effectively and efficiently
- `Optimal Product Placement`: good segmentation strategy will help the business to develop and offer new products
- `Categorize customers`: high revenue customers for better advantages, untapped customers, loyal customers.

## RFM Model
In the topic of Segmentation, we will introduce the RFM model, which is a popular model in marketing and customer segmentation for determining Customer Value (CV).

![[tutorial78.1.png]]

### What's R, F, M?
RFM segments customers by 3 important features:

1. `Recency`: to measure how recent the last interaction with a customer
2. `Frequency`: how frequent a customer interact with the business
3. `Monetary`: the monetary value a customer generate

- Normally, each element in `R`, `F`, `M` will be assigned a score from 1-5 (5 as the best), or value of low, mid, high. 
	- So that, the customer with `RFM score = 555` (R=5, F=5, M=5) will be very recently active, purchase frequently and strong purchasing power - 'spender' (high R, high F, high M) -> Most important group of users
	- On the other hand, `RFM score = 111` are customers who buy only one very cheap product for a long time ago.

### Discretization/Splits
- Notice that when we compute the R, F, M from the data (we will see in details via [[7-Lab-RFM]]), they are numerical values
	- For example of `Frequency` (as below), we count times of purchases, having value from 0 to 25. Then, we need to split them into 5 bins (similar to `age_group` in [[4-Lab-Metrics]])
	- How to do it?
- There are several ways to discretize R, F, M into bins:
	- Percentiles or quantiles (as in [[6-Lab-EDA-Viz]], from numerical to categorical)
	- Pareto Rule - 80/20: Split into `<80th percentiles`, and `>=80th percentiles`
	- Arbitrary/Expert Split (based on business acumen and experience)
	
![[tutorial78.2.png]]
- The number of splits are up to the DA (and inputs from business). We can also split by low/mid/high. 
	- From that, we will have a matrix as below, to classify customers into different groups, based on the combination of their segments in each factor of RFM

### RFM: Numbers of Segments
- When combine 3-level (low/mid/high) of `F` and `R`, we will have 9 segments, with colors to represent `M`
![[tutorial78.3.png]]

- Below, we can further divide or combine segments into 10-11 segments
![[tutorial78.4.png]]

- For each segment, the business will have different activities and actionable tips
![[tutorial78.5a.png]]
![[tutorial78.5b.png]]

- Below is the example that we combine different combinations of 3 digits in `RFM Score` to construct a segment. Potential Loyalties consists of `24 scores`
![[tutorial78.6.png]]

### RFM Variants
Notice that you do not need to stick with the classic RFM, there are multiple variants (you can also create one variants from your creativity):
- `RFD`: Recency, Frequency, Duration (for viewership/streaming business, such as Youtube, Spotify)
- `RFE`: Recency, Frequency, Engagement - A broader version, where Engagement is up to the design of business/DA
- `RFMTC`: Recency, Frequency, Monetary Value, Time, Churn rate - Augmented model

## Clustering
> The tasks of combining RFM Scores as in [[#RFM Numbers of Segments]] could become very complex. Especially when, we need to combine them in the way that we can have meaningful segments to take action. Machine Learning techniques come to place. 

![[tutorial78.7.png]]

- Input any customer features we have, and find out different clusters can be obtained from the data
- The process is by Machine Learning (`Unsupervised`). We can find traits of customer segments by analyzing the characteristics of the clusters
- Clustering could applied with R, F, M as inputs, or it could take more attributes of information about the customers
- Clustering is especially useful when many customer attributes, that make the combination of all possible segments is high, and the problem becomes complex

> Discussion: How is a good customer segmentation?

### K-means Algorithms
> Among numerous other clustering algorithms, we introduce the k-means clustering algorithms.

- K-means algorithms splits the records in the data into a pre-defined number of clusters (`k`)
	- Data points within a cluster are "close" to each others -> Similar
- How? 
	- Find the centroid (`mean`) of clusters
	- Minimize the distance between data points and the centroid within the clusters
- Need to decide `k` beforehand?
	- Back to the question: How we define a good segmentation/clustering?
		- How close the data points within the same cluter, compare to the distance with other clusters
		- Measure by `Silhouette Coefficient` (S-coef)
			- Range from `-1` to `1`
			- Closer to `1` -> Better
		- Measure by Total Distance - `Total Within Sum of Square`: distance between data points and the centroid within the cluster
	- `Elbow Method`: We try different value of `k` and plot the metrics of performance (Total Distance of S-coef), graphically define the Elbow Points -> Optional `k`
	![[tutorial78.8.png]]
	- Though sometimes, due to business acumen or practical reasons of execution, we will choose a desired `k` (for example, by method, k=10 is optimal, but execute 10 segments are not practical for business, we might choose 5-6 segments)

## Exercise
- [[7-Lab-RFM]]
- [[8-Lab-Kmeans]]

## References
- [RFM (market research)](https://en.wikipedia.org/wiki/RFM_(market_research)
- [K-means Codecademy](https://www.codecademy.com/learn/intprep-ds-machine-learning-algorithms-ds-interviews/modules/intprep-ds-unsupervised-learning-interview-questions/cheatsheet)
