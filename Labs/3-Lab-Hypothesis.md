Now, we will apply the Hypothesis-approach to analysis the *Kaggle* dataset [HR Analytics: Job Change of Data Scientist](https://www.kaggle.com/uniabhi/hr-analytics-job-change-of-data-scientists). 

> A company which is active in Big Data and Data Science wants to hire data scientists among people who successfully pass some courses which conduct by the company. Many people signup for their training. Company wants to know ==which of these candidates are really wants to work for the company after training or looking for a new employment== because it helps to reduce the cost and time as well as the quality of training or planning the courses and categorization of candidates. 

## Start with a Questions
Before jumping to the data, we can have some guess about the data:

<details> <summary>1.What is the big question of this analysis?</summary> 
	<p><b>Answer</b>: As the highlight in the introduction text, on a pool of candidates, we would want to know who will likely look for a job change after the training?</p> 
</details>

<details> <summary>2.What granularity you would expect for the dataset?</summary> 
	<p><b>Answer</b>: Each row would represent a candidate (enrollee)</p> 
</details>

<details> <summary>3.What information (as columns in the dataset) should we expect? <i>(Hints: Imagine what you would need to fill in the application form?)</i></summary> 
	<p><b>Answer</b>: Demographics (gender, age, address), Work Experience, Education</p> 
</details>

> Then, check the data dictionary [HERE](https://www.kaggle.com/uniabhi/hr-analytics-job-change-of-data-scientists)
> Move to `Data Explorer`, on the tab, try different view: `Detail`, `Compact`, `Column`

<details> <summary>4. What is the unique key of the table? 
	</summary> 
	<p><b>Answer</b>: As our guess in question 2, the unique key is <i>enrollee_id</i></p> 
</details>

<details> <summary>5. Check the columns we actually have? 
	</summary> 
	<p><b>Answer</b>:</p> 
	<li>Demographics (city, city_development_index, gender)</li> 
	<li>Work Experience (relevant_experience, experience, company_size, company_type, lastnewjob</li>
	<li>Education (enrolled_university, education_level, major_discipline)</li>
	<li>Other: training_hours, target</li>
</details>

> Now, check the details of each column, by move to `Column` View. 

![[lab3.1.png]]
- This is the standard output of the Summary of `EDA` ==(Exploratory Data Analysis)==. This will provide you the general information and basic statistics summary of the dataset. For each column, we have:
	- Number of valid (not-null) rows
	- Number of missing value rows
	- Mean, Std. Deviation, Quantiles (Will discuss in [[6-Data Manipulation & Viz Python]])
- On the left, we have a chart called ==Histogram==, with the count of rows by each distinct value of each column.
![[lab3.2.png]]
- Go through all columns and notice the values each have, and how the value distributed (most common value, average, etc.)

<details> <summary>6. What is most common type of enrolled_university? 
	</summary> 
	<p><b>Answer</b>: no_enrollment (72%), and full time course (20%)</p> 
</details>

<details> <summary>7. What is most common levels of education? 
	</summary> 
	<p><b>Answer</b>: Graduate (61%), and Masters (23%)</p> 
</details>

## Form Hypotheses
> Hypotheses are possible answers for a questions.

Now, let's fome some interesting hypotheses for the questions: ==Who will likely looking for a job change after trainings?==
1. `training_hours`: higher completed hours -> More likely change the job? 
	- `(Possible) Why(s)`: because the candidate have high motivations, relectings through their dedication for the trainings
2. `major_discipline`: STEM candidate -> More likely change the job?
	- `(Possible) Why(s)`: Because Data Science is close to STEM. They might be more compatible to Data Science job
3. `experience`: Less experience year -> More likely change the job?
	- `(Possible) Why(s)`: For more year in experience, it will more "painful" and costly to change the job. 
4. `gender`: Male -> More likely change the job?
	- `(Possible) Why(s)`: Female is more risk-adverse.

> Discussion: Any further possible hypotheses?

## Exploratory
- `Exploratory` is an drag-and-drop tool that enable us to explore data, discover insights, and communicate (charts)
- Download: [Exploratory Public](https://exploratory.io/download-public)
- Open `Exploratory`:
	- Create New Project
	- Input Data: `Data Frame` -> `File Data` -> Text File (CSV)
	- Select the Folder, and File Data(s)
	- Click `Import`
![[lab3.3.png]]

### Summary
- On tab `Summary`, we also have the Data Summary similar to the Summary Data Explore of Kaggle
![[lab3.4.png]]


### Data Wrangling
> The process of transforming, clearning, mapping the "raw" data to make it more appropriate for "downstream" purposes: Analytics, Modelling.
- `Exploratory` provides the drag-and-drop tools to do that without much coding (yet, `Exploratory` is built on top of R, in other words, it translate your instructions to R code and run behind-the-scene)
- Right panel, `Steps`, click `Add a new steps`
- Transform `major_discipline` into only 2 values: `STEM` and `Non-STEM`
![[lab3.5.png]]
	- Click on the card of `major_discipline` -> `Replace Values` -> `With Condition`
	- Enter the conditions
	![[lab3.6.png]]
	- Now, the value of `major_discipline` has change
- Similarly, we can transform other columns, such as: Replace all `NA` with `'None'`

> Combine and Split the values might help us to make the insights clearer

### Extract Insights
> Data has not much value, we need to convert it to insights.
- In [[1-Lab-SQL-Basic]], we learn about using `GROUP BY` and Aggregate Function to obtain insgiths. 
- In `Exploratory` (or any other tools: Excel, Pandas Python, etc.), we actually do the same techniques. 
- Let's use data to validate the hypotheses in [[#Form Hypotheses]] 

### H1 about `training_hours`
- Click on `Chart`
- Type: `Pivot Table`
	- Row: `training_hours` (Equivalent to SQL `GROUP BY`-> Exploratory will auto-split the value into bins) 
	- Value:
		- Number of rows (equivalent to SQL `COUNT(*)` in SQL)
		- `target` -> Choose: MEAN (equivalent to SQL `AVG(target)`)
![[lab3.7.png]]
- We have not significant clear pattern yet, as the target rate is similar among groups.
- Try to filter the training hour, we hope that with the smaller range (and so smaller bins) we could detect some pattens:
	- `training_hour` =< 100 (equivalent to SQL `WHERE`)
	- `training_hour` =< 50
![[lab3.8.png]]

> ==No Insight== We still cannot detect any significant trend between `training_hours` and probability of jumping job (target rate)

### H2 about `major_discipline`
- GROUP BY: `major_discipline`
![[lab3.9.png]]

> ==Insights==
> - STEM group have higher candidates, which make sense because it is closer for STEM to be interested and apply to Data Science training
> - They also have higher chance to change the job
- We can use Bar chart to visualize this insight
	- Type: `Bar`
	- X-Axis: `major_discipline`
	- Y-Axis: `target | MEAN`

![[lab3.10.png]]

### H3 about `experience`
- Filter: Remove NA
![[lab3.11.png]]

> ==Insights==
> - Consitent with what we expect, candidate with less than or equal 1 year are most likely to change job
> - Meanwhile, people with 16-20 year are much less likely to change

### H4 about `gender`
- Filter out `gender = 'None'` and `gender = 'Others'`
![[lab3.12.png]]
> ==Insights==
> - Surprisingly (different from our hypothesis), female candidate is more likely to switch. We might think that female is risk-adverse but once they apply for the program, they are serious with it

## Recap
1. Before jumping to the data, the understanding about the problem could help us imagine about the dataset
2. When receiving a new dataset, check granularity, columns, and mapping it with our expectation in (1)
3. Data Summary with information about Missing Value, Count, Min, Max, etc. would give us the first understanding about data
4. Form Hypothesis: based on available data we have, what interesting questions we could validate with the data?
5. Validate hypothesis: Group and Aggregate Function is used, then visualize to communicate the insights
6. `Exploratory` is introduced as an drag-and-drop tool that enable us to explore data, discover insights, and communicate (with no coding).