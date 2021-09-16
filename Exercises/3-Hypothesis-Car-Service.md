For [[Capstone Project - Car Service]], we will apply the hypothesis-based approach to analyze the case study and apply the data to build a product proposal.

## 1. Business Questions/Hypotheses
-   What are taxis used for?
-   What are the characteristics of the people that use them?
-   What are the existing pain points with taxis?
-   What factors might impact the profit of business?

Examples:
- Target users will be those who take a relatively longer time getting to a destination that is relatively close by, due to heavy traffic conditions and/or limitations to physical road infrastructure

## 2. Gather the Data
- Use dataset `./Data/tax_rides.csv` from Exercise [[1-SQL in pgAdmin]]
- Which fields (information) on this dataset could help you to answer the question in [[#1 Business Questions Hypotheses]]?
- Enrich dataset:
	- You notice that ride price is not included, but figure it could be derived. Based on information about New York taxi prices gleaned from the internet, create a calculated field called price using fixed variables, duration, distance, and passenger count
	- You hypothesize your target users will be those who take a relatively longer time getting to a destination that is relatively close by, due to heavy traffic conditions and/or limitations to physical road infrastructure. To be able to analyze where this is happening, you will need to create a calculated field called `distance-to-duration ratio`

## 3. Trends of the Data
For the MVP (or even for production launch) it may not make operational sense to have the service running 24/7.
-   What times throughout the day experience relatively higher volumes of ride pickups?
-   What days throughout the week experience relatively higher volumes of ride pickups?

## 4. Analyze User Research Survey Data
- Use dataset `./Data/user-research.csv`, with 10 columns for the following questions:
	1. What is your email?
	2. What gender do you identify as?
	3. What is your age?
	4. What is your annual income? (income bands)
	5. What neighborhood do you reside in?
	6. Do you currently use taxis? (Y/N)
	7. Do you currently use ridesharing services? (Y/N)
	8. Would you use a flying taxi service, if such a concept existed? (Y/N)
	9. If yes to Q8, how much would you be willing to pay per mile for such a service? (USD)
	10. If no to Q8, what is the reason?
- Import to `PostgreSQL` (or `Colab Notebook` and use `DuckDb` [[3-Lab-Hypothesis]]), write SQL to analyze and determine sentiment and user segments that would likely use a flying car service

## 5. Gather Data & Re-scope
- What further data you needs to validate your hypotheses?
- Try to find relevant datasets on https://www.kaggle.com/ (keyword: `Uber`)
- Eliminate the hypotheses that you cannot find the data to support
- Add more hypotheses that are inspried from the data available