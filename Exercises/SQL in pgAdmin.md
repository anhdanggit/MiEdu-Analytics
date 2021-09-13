On `PostgreSQL` and `pgAdmin`

## 1. Zoo Database and Tables
1.  Imagine you’re building a database to catalog all the animals at your local zoo. You want one table to track the kinds of animals in the collection and another table to track the specifics on each animal. Write `CREATE TABLE` statements for each table that include some of the columns you need. Why did you include the columns you chose?
2.  Now create `INSERT` statements to load sample data into the tables. How can you view the data via the pgAdmin tool? Create an additional `INSERT` statement for one of your tables. Purposely omit one of the required commas separating the entries in the VALUES clause of the query. What is the error message? Would it help you find the error in the code?

## 2. Import CSV and Analysis
- Upload the dataset `./Data/tax_rides.csv` to Postgre
- Get the high-level understanding of the granularity and scope of the dataset:
	1. How many records are in the dataset
	2. What does each record represent?
	3. What is the primary key?
	4. What date range is your dataset bound to?
	5. What are the geographical bounds of this dataset?
- Write SQL to analyze the distribution of the dataset: Calculate the average, median, and the first & second standard deviation of the mean for the following measures:
	1. duration
	2. distance
	3. passenger counts
	4. duration-to-distance ratio
	5. price
