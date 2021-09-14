## Data Modeling & SQL
- #data-modelling: How data is structured by tables, and relationship between them (`keys`) -> How we aggerate data? 
	- Table: grid of rows and columns that store data
		- Granularity (what does each row represent?) -> Rows = Records/Observations which have a collection of columns (features/fields -> Charactertized for each observations)
		- Each columns: contains specified data type (numbers, characters, dates)
- #sql is not only the mean to extract data, it's to define the structures that hold data, including tables, and the relationship between table 

## DWH
![[tutorial1.1.png]]
- Sources of Data comes from the underneath system that generates data that records the states of operations and transaction of the business
	- `ETL`: Extraction, transform, loading
	- `EII`: Enterprise Information Integration
	- `DWH`: Data Warehouse that enables the reportings, visualizations, OLAP (Analytical Processing) of the business
- The data from DWH is to construct key metrics of the business -> [[Metrics Design]]

## Working with DWH
- Understanding tables is fundamental to understanding the database 
	- Table name?
	- Column structure: text, numbers, id?
	- Number of rows?
- Why we need to split separate tables?
	- Seperate tables to reduce redundant data -> Don't waste database space
	- Expand the data by mapping across tables (through keys)
	- Tables are the core building block of every database
- **Granularity**: what does each row represent?
	- *Unique Key*: identify each row (unique for each row, every valid row/record should have a key) -> This is the key to map different tables in Database -> ==This is the power of a relational database== 
- **Fact-dimensional table**:
	- *Fact tables*: data that is being measured or counted
	- *Dimensional table*: metadata, about diffent slices in fact table 
	- Join by common keys

## Set-up PostgreSQL in pgAdmin
- Install `PostgreSQL`
	- Windows: [EDB Download](https://www.enterprisedb.com/software-downloads-postgres)
	- macOS: [Postgreapp Download](http://postgresapp.com/)
- Download & Install `pgAdmin`: https://www.pgadmin.org/download/
- Run `PostgreSQL` 
- Run `pgAdmin`
- Connect `pdAdmin` to the default database
	- Left vertical pane -> `Object Browser`: where you can view available servers, database, users, and other object
	- Righ-click -> `Create` -> `Server`:
		- (On `General` Tab):
			- Name: `PostgreSQL 13` 
		- (On `Connection` Tab):
			- Host name/address: `localhost`
			- Password: `<YOUR-PASSWORD>`
			![[tutorial1.2.png]]
- Now, you will see all the available components under the default server of PostgreSQL
	![[tutorial1.3.png]]

## Execute SQL in pgAdmin
1. Run `PostgreSQL`
2. Launch `pgAdmin` -> Click on the `Server` and `Databases`
3. On `Menu` -> `Tools` -> `Query Tool`

## 1. Create the DATABASE
- In the *Query Editor Query*, Create the DB: `CREATE DATABASE analysis;`

## 2. Create the TABLE
- Create the table named `teachers` with 6 columns
```
CREATE TABLE teachers (
	id bigserial,
	first_name varchar(25),
	last_name varchar(50),
	school varchar(50),
	hire_date date,
	salary numeric
);
```
- To Drop the table: `DROP TABLE teachers;`

## 3. INSERT DATA
- Insert data
```
INSERT INTO teachers (first_name, last_name, school, hire_date, salary)
VALUES 
	('Janet', 'Smith', 'F.D. Roosevelt HS', '2011-10-30', 36200),
	('Lee', 'Reynolds', 'F.D. Roosevelt HS', '1993-05-22', 65000),
	('Samuel', 'Cole', 'Myers Middle School', '2005-08-01', 43500),
	('Samantha', 'Bush', 'Myers Middle School', '2011-10-30', 36200),
	('Betty', 'Diaz', 'Myers Middle School', '2005-08-30', 43500),
	('Kathleen', 'Roush', 'F.D. Roosevelt HS', '2010-10-22', 38500);
```
- Check the data: `SELECT * FROM teachers`
	- The `id` is auto-added to the `teacher tables`
	![[tutorial1.4.png]]
	- Check the *Unique key*
	![[tutorial1.5.png]]
	
## 4. Common SQL 
- SQL Cheat Sheet: [LINK](https://www.sqltutorial.org/sql-cheat-sheet/)
- Supplement: [02-Data-Collection/SQL](https://github.com/mieduai/fsds-courses/blob/master/02-Data-Collection/03-sql.ipynb)

## Import CSV 
- A row-by-row `INSERT` is useful for making quick test tables or adding a few rows to an existing table
- You might need to loac the batch (hundreds, thoursands rows) -> *delimited text file*
> 1. Prep the source data in the form of a delimited text file.
> 2. Create a table to store the data.
> 3. Write a `COPY` script to perform the import.

### 1. Create the table to store data
```
CREATE TABLE supervisor_salaries (
	town varchar(30),
	county varchar(30),
	supervisor varchar(30),
	start_date date,
	salary money,
	benefits money
);
```
### 2. Import data
```
COPY supervisor_salaries (town, supervisor, salary)
FROM 'C:\YourDirectory\supervisor_salaries.csv'
WITH (FORMAT CSV, HEADER);
```
- Check: `SELECT * FROM supervisor_salaries LIMIT 10`

## Data Distribution
- How spread apart is the data, and where the center of the data lies in terms of both the weight numerical values, and the raw number of data points?
	- **Standard deviation**: the measure of how dispersed (wide) a dataset is
	- **One standard deviation from the mean**: 68% of all values fall by this range both below and above the mean
	- **Two standard deviation from the mean**: 95% of all values fall by this range both below and above the mean
	- **Median**: Midpoint of a frequency distribution
	- **Average** - Also known as mean, the value of the central point in a dataset based on the weight other values
	- **Long tail distribution** - When a distribution has large number of occurrences that are relatively far from the median of the distribution
- Supplement: [Statistics 101](https://www.youtube.com/watch?v=8YLvF5ztH90)

## Exercies
[[SQL in pgAdmin]]

## References
- [[Debarros (2018). Practical SQL]]
- [[1 - Data Modelling & Metrics]]
- [Github Practical SQL](https://github.com/anthonydb/practical-sql)