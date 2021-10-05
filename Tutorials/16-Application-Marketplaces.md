
Two-sided marketplace is the variation of [[15-Application-Ecommerce]]. 

## What is Two-sided Markeplace?
- The business makes money when a buyer and seller come together to complete a transaction
- Other two-sided market:
	- Real Estate Listing
	- Indiegogo
	- eBay
	- Dating sites: inventory = prospective partners
	- Shopee
	- Now / Foody
	- Grab Food
	- Online Labour Job Market (Freelancer)
	- Peer-to-peer lending
	- Kickstarter
	- Grab
	- Uber
	- Amazon

![[tutorial16.1.png]]

### Components of Marketplaces
- Shared inventory
- Two-sided stakeholders:
	- Buyers vs. Sellers
	- Creators vs. Supporters
	- Drivers vs. Riders
- Buyers/Seller are differentiate by set of attributes, tastes, parameters (ratings, pricing, location, etc.)
- ==Business in the intermediate and make the money when two-side come together==
	- They face the unique problems of marketplaces: ==Attract and satisfy buyers and users at the same time==

### Narrow-down the "marketplace"
> We might have some criteria to determine the marketplace, though this is a narrow-down definition, which might exclude some models of marketplaces as mentioned above. ==In reality, there is no black-and-white as we have many innovative business model.==
- Sellers is responsible for listing and promoting the product
	- For this, Tiki and Amazon is not within our scope, but Shopee is a marketplace
- Marketplace owner (the business) has a "hands off" approach to the individual transactions
	- For this, Uber is not within our scope
- Buye and seller have competing interests -> ==Sellers wants to make as much as money as possible, while buyers wants to spend as little as possible== -> This make the problem of marketplace to satisfy both sides as mention in [[#Components of Marketplaces]] a challenging mission
	- For this, dating apps is not within our scope, as they have mutual interest

> Though some concepts about markeplaces are still applied for relevant business models (Creativity will work in reality, due to the fact that we have more and more innovative models, it needs to combine several traditional models to analyze modern business)

## Case Study: DuProprio
> DuProprio/Comfree is the largest for-sale-by-owner marketplace, and second-most-visited real estate network in Canada. Founded in 1997 by co-president Nicolas Bouchard, it lists 17,000 properties and has roughly 5 million visits a month. The company charges a one-time fee of around $900 for a listing, assistance with pricing, signage, and HDR photography. Additional tools, from legal advice to real estate coaching, are available for an extra fee. The company also has affiliate listing relationships with a prominent newspaper.

![[tutorial16.2.png]]

This is a good example to illustrate the Lean Analytics Cycle, we learn in [[5-Analytics Frameworks]]:
- The business moves through several stages as it grows
- At the beginning, the analysis is simple, insights could be rule-of-thumbs (line-on-sand)
- The analytics envolved over time, hand-in-hand with how the business/product becomes more sophisticated
- The metrics tracked over time changed -> Metrics is used to set the goal of the business at each stage:
	- **Early stage**: focus on `Acquisition` -> Goal is to have as much listings as possible
	- **Stage 1 (Seller Login)**: focus on visitor-to-listing (number of search, page hites, page trends) -> Goal is to attract buyers
	- **Stage 2 (Buyer Login)**: Focus on conversion rate (subscriber/visitors) -> `Activation` 
	- **Emerging**: Focus on Revenue metrics (list-to-sold), average package size, number of sales -> `Revenue` (not in the graph)

> Discussion: Do we need retention in this model? If Yes, on Buyer or Seller side? Propose the metrics to measure?

### Summary
- On early stage, the marketplace can grow inventory by hands (low-tech approach, i.e static website) -> This could be enough for big-ticket, slow-turnover marketplaces (real-estate)
- Some marketplaces charge by per-listing, or per-transaction fee, rather than commission
- Then, build buyer attention, this will convince sellers to join them (though, it is chicken-and-egg)
- Ultimately, volumne of sales, value per transaction, resulting revenue is the only metrics that matters (but it's premature to optimize at the early sateg)

### Analytics Lessons
- Start with a minimum marketplace to prove that we have a demand, supply, and interest from both buyers and sellers
- At the early stage, the metrics is more about awareness: Number of Listing (for seller side)
- Later, the marketplace becomes more dynamic, metrics describe:
	- `Buyer activity`: listing, update
	- `Seller activity`: search, set criteria, notification
	- `Transaction`: size of package, convert to sold

## Chicken-and-Egg / Supply-and-Demand
- To ==avoid "chicken-and-egg"==: Because the marketplace is not a supplier, not a buyer, you need to ensure the sufficient supply/inventory AND sufficient group of buyers -> Depend on selling categories, pick which side of the market you're going to "seed":
	- ==Seed in "seller-side"==: 
		- Approach and check their inventory, persuade them to list items
		- "Purchase" some or take order with a promise of fullfilling tehem later -> To win the access to that inventory:
			- Uber buy up available towcars: at the beginning they paid drivers, then switching to commission model when they already have sifficient demand to make it worthwhile for drivers
	- ==Seed in "buyer-side"==: 
		- Set-up a forum, community to bring "buyers" together and invite them to social sites
		- =="Seeding inventory"==: start with the inventory you create and gradually adding inventory from others
			-  Amazon: started selling (as a seller), which allows them to build-up its order, search, and logistrics process

### Health Metrics of "chicken-and-egg"
> The first thing of two-sided marketplace (and the first thing to measure) is the ability of the business to create inventory (supply) or buyers/audiences (demand)
- Uber:
	- `Driver Earnings` - How much drivers make on a commission basis (share of charge per ride)
	- Inventory: `Time to pick-up`
- Amazon:
	- `Number of retained book buyers`, who satisfy with the purchase and delivery process

> Discussion: How to solve chicken-and-egg in Tinder? Recommend the health metrics?

## Impacts & Metrics
### Tracking Buyers / Sellers
- The value of a marketplace is the diversification and volumns, so it can increase the likelihood of transaction
- In both sellers and buyers side, below are example of some =="vanity metrics"==, that do not add much value. You can increase the value of metrics ([[4-Metrics Design]]) by:
	- ==Draw-the-line==: "Narrow-down"/stricten the scope of metrics that enables us to use the metrics to differentiate good/bad customers
	- Ratio and Rate: So we focus on growth (rate) and ratio (proportion) and not blind by the total number

![[tutorial16.3.png]]
- ==Buyer/Seller Growth==: Once we have different metrics to count buyers, we can compute the growth rate over time to know how fast and how slow we move
	- Seller-base:
		- Grow well: ready to onboard more buyers
		- Slow: spend more effort and money to find new sellers -> Focus on increase the number of listing
	- Buyer-based:
		- Grow well: good news, but cautious about lack of inventory
		- Slow: promotion, attract more buyers
- ==Inventory Growth==: Above we only count the seller, we also need to track listings they create -> `Number of Listings per Seller`

### Funnel of Transaction
![[tutorial16.4.png]]
The funnel of transaction on the markeplace follows these steps:
- Search -> Match -> Click-through to Listing -> Purchase -> Satisfied transactions
	- Follow that it has the metrics as in the graph above
- ==Search Effectiveness==:
	- Buyer Search counts:
		- Demand: `Daily search per buyer`
		- Supply: `Daily new listing`
		- Match: `Average Result per Search`
- ==Conversion Funnels==
![[tutorial16.5.png]]
- ==Ratings & Frauds==: buyer/selle rating, flag if something wrong
	- Percent of Listing flagged (Fraud)
	- Percent of Sellers rated below 3/5
	- Percent of Buyers rated below 3/5
	- Percent of ratings
- ==Pricing Metrics==: if you have a bidding methods on the marketplace (eBay), we need to track the pricing, to make sure sellers not set too high price

> Discussion: 
> - Describe the funnel of transaction for Grab. What metrics to measure?

## Visualize Two-sided Marketplace
> Below represents a user's flow through an e-commerce business, along with key metrics at each stage?
![[tutorial16.6.png]]

> Discussion:
> -   Interpret the visuals above
> -   If you join an marketplace business, what analysis you will propose? To answer which questions?

## Recap
- Two-side markets come in all shapes and sizes
- On th early stage, the big challenge is to solve [[#Chicken-and-Egg Supply-and-Demand]] problem
- We need to track buyers, sellers, inventory growth, and also how well it fit buyers demands - Search Effectiveness, Conversion
- Two-sided markeplaces are a variant of E-commerce. We focus on the metrics that make a marketplace different, though many metrics in E-commerce (See: [[15-Application-Ecommerce]]), could be applied for marketplace.