> Where the [[Metrics Design]] come from? This part will provide some common frameworks to design set of KPIs for (almost) any business model. With metrics as the ingredients, analytics techniques come to place to explain the relationship between the metrics, to uncover the hidden patterns and trends.

## Lean Analytics Cycle
![[tutorial5.2.png]]
1. Start with a KPI to measure a business factor that we want to improve
2. Create the baseline 
3. How to go beyond the baseline:
	- No historical data: Ideation, Qualitative research
	- Historical data:
		- Define "Good" Customers
		- What these "Good" customer have in common? -> [[Segmentation Analysis]]
		- What of their attributes correlate with the KPI? -> [[Regression Analysis]]
4. Take Action:
	- Experiments
	- Make changes in the business
5. Measure Effect of Actions
	- [[A/B Testing]]
	- [[Cohort Analysis]]
6. If the effect is improved?
	- Yes? Move to new KPI to improve
	- No? -> Revisit (2)

## Analytics Framework
- `Analytics Framework` starts with designing set of metrics to measure different aspects of business
- Ideally, set of metrics should reflect, and so be mappable with components of business. 
- Introduce 2 common model to map business with set of metrics

### Pirate Metrics
![[tutorial5.3.png]]
- This framework is designed to measure 5 elements of a successul business: Impression -> Click -> Install -> Generate Revenue -> Viral
- `AARRR`: Acquisitions, Activation, Retention, Revenue, Referral
- For each elemen, we have relevant metrics
![[tutorial5.1.png]]

### Lean Canvas
![[tutorial5.4.png]]
- Lean Canvas is another framework that decompose the state of business into boxes: Problem, Solution, Unique Value Proposition, Customer Segments, Channels, Unfair Advantage, Revenue Streams, Cost Structure
- For each box, we have relevant metrics

### Generalize
Even though we have many framework to decompose the business, there are overlap in stages of Analytics, and metrics to measure the performance of business.

![[tutorial5.5.png]]

## Analytics Techniques
![[tutorial5.6.png]]

- While the metrics enables us to quantify factors, analytics techniques help to uncover the relationship between metrics, and investigate the changes of each metrics given the conditions described by other metrics
- In [[#Lean Analytics Cycle]], we highlight some key tasks that employ some common techniques:
	- *Segmentation*: split the population into smaller group share common characteristics. By that, we could zoom-in and have a better view and insights about different sub-groups
	- *Cohort Analysis*: compare the group through their whole life-cycle (i.e. customer cohorts acquire at different time) -> Enable us to explore the pattern by stages in theri cycle (i.e. existing users will behave different from new users) 
	- *A/B Testing*: to fairly evaluate the impact of an intervention
	- *Regression Analysis*: to evaluate the impact of changes in different factors on the outcome
	- *Time-series Analysis*: to forecast and investigate the trends and patterns of a metric over time.

## Exercise
[[Analytics-Framework-Apps]]
