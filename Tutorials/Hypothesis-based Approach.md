> Analytics is all about uncover the hidden stories in your data

All the techniques in this course, together with SQL, Python and other tools is to serve the purpose of analytics. This will give your the tools to find interesting trends, insights, or anomalies in your data, and support to make a smart decision. 

We will learn about the process to discover the stories in data and communicate the findings. This will be so far the foundational process to connect all topics we learn.

## Data-Driven vs. Data-Informed
-   Data is a powerful thing. It can be additive, making us over-analyze everything
-   Also wisdom and experience, rather than rigid analysis
-   Data is a tool, not a shortcut that we don't have to do a hard work (pick-up phone and call customers)
-   Data tells us what already happen, theories inspire us what have never happened before. Data ⇒ Local optimize

> Humans do inspiration; machines do validations. Data optimize a know system, humans are good at finding a new one.

-   Ultimately, quantitative data is great for testing hypotheses, but it’s lousy for generating new ones unless combined with human introspection.

## Start with a Question (Hypothesis)
![[tutorial3.1.png]]
- Curiosity, intuition, or sometimes just dumb luck can often spark ideas for data analysis. 
	- *Observe*: notice changes around you over time
	- *Measure*: how to quantify the change
	- *Question*: Question create the opportunity for data analysis to find good findings, and discover a new business opportunity
- Keep track these ideas and prioritize them by potential value
- Questions come first, data come after to validate: the questions and hypothesis about answer will help you to narrow down your analysis
	![[tutorial3.3.png]]
	
## Document the Process
- To make sure your findings and work could be inherited and reproducible by others, document enough information
- Take notes
- Git: version control 

## Gather Data 
- Browse the Database (follow the steps in [[DWH & SQL]]): Understand tables and structures
- Source of Data: understand where the data comes from, and the characteristics of the data sources 

## Query Data
- Explore Database by queries
- Start with aggregates: `Counts`, `Sums`, `Sort` and group by different granularities

## Consult Data's Owner
- After querying data and have early insights about the trends and patterns, bring any concerns and questions to a person who knows the data well (your stakeholders or Data Engineer Team)
- Goals:
	- Understand the limits of data
	- Make sure you have a good sample (no missing)
	- Determine if the dataset suits your needs

## Key Indicators and Trends over Time
- Goal: To unearth data that you can summarize in a sentence or present as a slide in a presentation (e.g: In the last 6-month, the revenue is increased by 10%)
- Two-step process:
	1. Choose an indicator to track (e.g: Revenue, profit margin)
	2. Track that indicator over multiple periods
	![[tutorial3.2.png]]

## Ask Why
- Data analysis can tell you what happened, but it doesn’t usually indicate why something happened 
- For the findings they can corroborate, ask them to help you understand the forces behind those findings

## Communicate Findings
- Identify the theme based on your findings
- Present the general trend
- Highlight specific example that support the trends
- Acknowledge examples counter the overal trends
- Stick to the facts: Avoid distorting or exaggerating any findings, or input your opinions rather than the facts
- Expert opinions
- Visualize numbers by charts
- Cite source of data / sample for analysis (time window, sub-population)

## Exercise
[[Hypothesis-Car-Service]]