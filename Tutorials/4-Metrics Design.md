## Metrics Design (OKR-KPIs)
> The data from [[1-2-DWH & SQL]] is to construct key metrics that the business reports and monitors. From [[3-Hypothesis-based Approach]], we quantify factors in hypothesis by metrics. The introduction of OKRs and KPIs is to illustrate why the metrics is the fundamental of the connection between business and data

### OKRs
-   **Objectives and Key Results (OKR) Framework**: goal setting methodology to define & track the progress towards objectives
-   **Objectives**: clearly defined goal at a high-level. Could be both quantitative or qualitative.
-   **Key Result**: `metric targets` that directly influence the success of meeting our objective. Could be both quantitative or qualitative, but usually the former. Multiple key results could roll up to one objective.
![[tutorial4.1.png]]

### KPIs
**Common KPIs Product Managers Leverage** (These KPIs are widely-used, but generic, you should tailor your KPIs to correlate with the success of your specific product depending on its use cases).

> Below we introduce some common KPIs in Mobile Apps Industry

**Business Operations KPIs**
-   `Monthly recurring revenue (MRR)` - total amount of estimated revenue that a company will receive on a monthly basis. Could be referred to as annual recurring revenue (ARR).
-   `Customer Lifetime Value (CLTV or LTV)` - total amount of money a customer is estimated to spend on your products or services during their lifetime
-   `Customer Acquisition Cost (CAC)` - total cost of marketing spent to have potential customer to buy a product or service

**Subscriptions KPIs**
-   `Retention rate` - the percentage of subscribers who are still subscribed after a specified time period
-   `Churn rate` - the percentage of subscribers who are no longer subscribed after a specified time period (inverse of retention rate)
-   `Number of active subscriptions` - the current number of active paying subscribers, that are not expired
-   `Trial to paid conversation` - the percentage of total subscription trialers that end up converting to a paid subscription after their trial ends

**Engagement KPIs**
-   `Number of sessions per user` - one session refers to any user doing activities on a product or a website, capped by exiting the website or by a period of user inactivity
-   `Number of user actions per session` - how much activity happens in each session
-   `Session duration` - the average time spent on each session
-   `Daily Active Users (DAU)` - number of unique users per day
-   `Monthly Active Users (MAU)` - number of unique users per month
-   `DAU / MAU` - a ratio effective at measuring the rate of stickiness, for products that are meant to be used daily
-   `Lness` - This number of days a user has used a specific feature in a given time frame. For example, L10+/14, represents the percentage of users who have used the specific feature in the past 2 weeks (or 14 days).

**Marketing KPIs**
-   `Click-through rate (CTR)` - percentage of people who click on an ad, or link in an email campaign, out of the total people who have seen it
-   `Customer Acquisition Cost (CAC)` - total cost of marketing spent to have potential customer to buy a product or service

**Referral KPIs**
-   `Net Promoter Score (NPS)` - a metric that estimates the loyalty of a customer and how much they would be willing to refer your product to other people (administered through customer surveys, with a score range from -100 to +100)
-   `Customer Satisfaction Score (CSAT)` - a metric to gauge customer satisfaction (usually administered after a customer support engagement, with a range from 0 - 100%)

## Good Metrics
-   `Comparative`: over time periods
-   `Understandable`: people can remember and discuss (data ⇒ action ⇒ culture)
-   `Ratio or a rate`: Easier to act on, Comparative, Good to comparing factor that somehow opposed (`Operating Expenses / Net Sales`)
-   A good metrics changes the way you behave
-   Metrics often come in pairs: Conversion rate <> Time-to-purchase

## Reporting Metrics vs. Exploring Metrics
-   `Known-known`: Facts to double-check with data
-   `Known-unknown`: Reporting metrics (KPIs) => Business Intelligence
-   `Unknown-known`: Intuition that we want to quantify, hypothesis-base analytics approach => Analytics
-   `Unknown-unknown`: Exploring (R&D) => Data Science
    
![[tutorial4.2.png]]

## Leading vs. Lagging Metrics
-   `Leading`: Number of prospects on sales funnel (⇒ Numbers of new users)
-   `Lagging`: churn - By the time you collect the data, it's too late. It's already happened

![[tutorial4.3.png]]
![[tutorial4.4.png]]

## Exercise
[[4-Metrics-Car-Service]]
