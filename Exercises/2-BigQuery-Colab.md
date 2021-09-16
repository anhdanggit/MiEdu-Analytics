## 1. Google Colab
- Get yourself familiar with Google Colab, run through these notebook
	- [Overview of Colaboratory Features](https://colab.research.google.com/notebooks/basic_features_overview.ipynb)
	- [Colab Import and Export Files](https://colab.research.google.com/notebooks/io.ipynb)
- Create a Colab Notebook
- Follow the guideline in [[2-Lab-SQL-BigQuery-Colab]], connect to BigQuery

> Use SQL to create the output for the below questions. Add notes and comments to the results
	
## 2. BigQuery: Austine_Bikeshare
- Data set: `bigquery-public-data:austin_bikeshare`, with two tables: `bikeshare_stations` and `bikeshare_trips`
- **Understand the granularity & schema**
	- In each table, what each row represent?
	- Identify *unique keys* of each table? -> Check by SQL if the key is unique for each row
	- Identify keys to map between two tables?
- **First Explore**
	- Table `bikeshare_stations`:
		- How many distinct values of `status`, `property_type`, `power_type`?
		- When the information about the station has been modified?
		- How many stations in the datasets?
	- Table `bikeshare_trips`:
		- How many distinct values of `subscriber_type`?
		- How many trips in the dataset?
		- Average durations of all trips?
- **GROUP BY & SUMMARIZE**
	- What is the most popular *Start Station*?
	- What is the most popular *End Station*?
	- What is the most popular route (pair of *Start Station* - *End Station*)?
	- For each route, how many trips, and the average duration?
	- By each `property_type` of station, what are the average length and width?
	- What is the largest station? (Hint: Compute the area from the existing columns)
- **JOIN**
	- Let's join 2 datasets together
	- How many percent of trips is having `solar` station as Start or End?
	- How many percent of trips by each `property_type` of *Start Station*
- Come up with your own questions and analyze the data
