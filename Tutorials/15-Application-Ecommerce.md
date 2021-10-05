E-commerce is where a visitor buys something from a web-based retailer. Amazon, Walmart.com, Tiki, etc. This is probably one of the most classical business model that analytics tools aim to (as it's one of the earliest business model in internet-based, with the significant data).

> This tutorial is to discuss some of the most important metrics of E-commerce, as well as some issues that make the analytics more complex.

## What Mode of E-commerce?
> Define Mode (Data/Metrics) -> Strategies -> Monitor the imacts (Data/Metrics)

### 1. Define Modes
- Metrics: 
	- `Annual Repurchase Rate (ARR)`: users last year will repurchase this year
	- OR `90-day repurchase rate`
- **Acquisition Mode**:
	- `<40%` ARR ~ `1-15%` 90d Repurchase rate
	- `70%` e-commerces in this category even when they are mature
	- Focus on new users acquisition, loyalty program might not be a good idea
- **Hybrid Mode**:
	- `40-60%` ARR ~ `15-30%` 90d Repurchase rate
	- Mix: new-returning users
	- Focus on both acquisition and increase purchase frequency
- **Loyalty Mode**:
	- `>60%` ARR ~ `>30%` 90d Repurchase rate
	- Focus on loyalty, returning users
	- Benefit, promotions, loyalty programs to encourage people buy more frequent.

- This could be the nature of business, value they offer and the target users:
	- Grocery: loyal, frequent
	- Climbing, Scuba: buy one time, but not get hooked to the hobbies that they need to upgrade -> Not frequent, questionable loyal
	- Eye glasses: rarely buy 5-10 glassess per year -> Not frequent, maybe loyal
	- Luxury: not frequent

> Discussion: 
> 1. Guess how they could come up with the above benchmarks?
> 2. Why they need both Annual Repurchase Rate, and 90-d repurchase rate?

### 2. Business Strategies
> What mode and relationship we have with customers will determine almost everythign else.
- Acquisition: 
	-  Need a large number of new customers with low cost -> Concentrate the marketing efforts on affordable customer acquisitions
	-  Long funnel (tweet, video, link) converted to a purchase -> Need to know what messages, what platforms generate the visitors who will buy things
	-  Once they are on the site, try to get as much as possible (Recommendation)
	-  Awareness / Virality (Referral: Eye glasses)
	-  ==Pricing Strategy==: might get only once chance to extract revenue from customers
-  Loyalty Mode:
	-  Focus on returning
		-  Do everything to keep payment simple: `One-click purchase` (Amazon)
		-  Wishlist + Review (User-generated Contents)

> Discussion: How we define/measure a good business strategies? What do we try to optimize?

### 3. Impacts & Metrics
- For efforts, the outcome is operating profit
![[tutorial15.1.png]]
- Though we need other metrics to measure, monitor and red flag different aspects of the process:
![[tutorial15.7.png]]


## Case Study: WineExpress.com
Some business in early stage is willing to take the operation loss/break-even, they will focus more on `Revenue per Customer`
- Trade-off: Optimize the Conversion Rate (Price reduction, promotion) but not benefit or even negative impact on revenue (due to the shopping cart size or frequency)

### 1. AB Testing on Page
![[tutorial15.2.png]]

### 2. Original Layout
- Original Layout
![[tutorial15.3.png]]

### 3. Winning Layout
- Winning Layout: Not focus too much on $0.99 offer
![[tutorial15.4.png]]

### Summary
- Real-life example of using [[10-AB-Testing]] to find a better-converting page 
- 'Better' not necessary the highest conversion 
- Conversion went up (leading metrics [[4-Metrics Design]]) but it need to lead to increase in revenue per visitor.
### Analytics Lessons
- Page Optimization is important
- Make sure to optimize the right metrics (`revenue per visitor`, not the `conversion` only)

## Other topics
- Keywords and search terms:
	- Which keywords drive traffic that turns into money
	- **Paid search**: bidding for popular keywords in search engine (as in the dashboard AARRR in [[5-Lab-Pirate-Metrics]])
	- **Organic search (unpaid)**: add compelling and desirable keywords into your content that will improve your ranking (search enginer will favour because of your relevance)
	- Analyze the ==search within your side==:
		- Do you have what people wants?
		- Popular categories that customer search -> Modify the positioning, feature the categories/products
- Recommendation Acceptance Rate:
	- Algorithms: 
		- Suggest other products similar to the one the visitor buy
		- Suggest the products that the visitor likely will like

> Discussion: 
> - What metrics we should design to measure the performance of search keywords?
> - What metrics we should design to measure the performance of recommendation?

## Online & Offline Combination
Though the purchasing in happen on web-based, there are offline factor that will impact the conversion rate, revenue per customer, and customer satisfactory in general (frequency, loyalty): shipping cost, shipping time
- **Shipping Time**: 
	- Customers become more demanding: real-time, within 2-hour, next-day shipping
	- How effectively the retailer handle logistics -> Operational Efficiencies
- **Stock Availability**:
	- "When items are out of stock, sales go down"
	- Improve Inventory Management -> Timely restock
	- Hide the out-of-stock from customers (search, website)
	- Analyze inventory vs. sales:
		- While the capacities of storage is constraint
		- Hold too much inventoy for things that don't sell well, but not enough for things sell weel
		- Proportion of product categories to how much they make up the sales

> Discussion: 
> - What metrics we should design to measure the aspect above?
> - What analysis we would propose: Questions, Hypothesis, Techniques?

## Visualize the E-Commerce Business
> Below represents a user's flow through an e-commerce business, along with key metrics at each stage?

![[tutorial15.6.png]]

> Discussion: 
> - Interpret the visuals above
> - If you join an e-commerce, what analysis you will propose? To answer which questions?


## Recap
- It's vital to know the mode of your e-commerce (this is the mix of the nature of business values and the target customers): Acquisition, Loyalty, or Hybrid
- The mode of your business will drive marketing, product, pricing strategies and features we want to add for customers
- While `conversion rate`, `repeat purchases` and `transaction size`, all of them are inputs to the ultimate goal: `revenue per customer` -> `revenue per customer` x `Volume of customer` = `Revenue`
- Don’t overlook offline considerations like shipping, warehouse logistics, and inventory.