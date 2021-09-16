## Set-up
- See the tutorial: [[1-2-DWH & SQL]], to set up `PostgreSQL` and `pgAdmin`
- Import these dataset into the `analysis database`
- On the left panel of pgAdmin, you will find all tables available under: `Databases/analysis/Schemas/public/Tables`
![[lab1.1.png]]

## 0. Import Data
> Import the dataset `./data/taxi_rides.csv` to PostgreSQL
```
CREATE TABLE taxi_rides (
	id varchar(30),
	vendor_id varchar(30),
	pickup_datetime timestamp,
	dropoff_datetime timestamp,
	passenger_count integer,
	pickup_longitude numeric,
	pickup_latitude numeric,
	dropoff_longitude numeric,
	dropoff_latitude numeric,
	store_and_fwd_flag varchar(30),
	duration numeric,
	distance numeric
);
```
```
COPY taxi_rides (id,vendor_id,pickup_datetime,dropoff_datetime,passenger_count,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude,store_and_fwd_flag,duration,distance)
FROM '/Users/danghuynhmaianh/Desktop/taxi_rides.csv'
WITH (FORMAT CSV, HEADER);
```
```
SELECT *
FROM taxi_rides
LIMIT 10
```

## 1. Basic Data Exploration
> What "everything" we have?
### SELECT *
Fetch every row and column in table `teachers`
- `*`: wildcard, represent for `everything`
```
SELECT *
FROM teachers
```

### Query Subset of Columns
> What "columns" are more interesting than others?
- Add any `column_name` after `SELECT`
- Explore the columns of tables on `pgAdmin` left-panel, under the table -> `Columns`
![[lab1.2.png]]
```
SELECT 
	last_name, 
	first_name, 
	salary 
FROM teachers;
```

### Granularity - DISTINCT value
> What each row represent for?
- The `id` on how we `CREATE TABLE` in [[1-2-DWH & SQL]] -> `teacher_id`
- We use the `DISTINCT` to only output the distinct (non-duplicated) value of a column
- Notice the school is duplicated in `teachers` (one school might have many teachers)
```
SELECT DISTINCT school  
FROM teachers;
```
- `DISTINCT` works with more than one column
- Answer the question "For each x (school) in the table, what are all existing y values (salary)"

```
SELECT DISTINCT school, salary  
FROM teachers;
```

### Sort Data - ORDER BY
> Who get the highest salary?
- Ordering is one simple technique to reveal pattern in data
- Add `DESC` for ordeding descendingly the data
```
SELECT first_name, last_name, salary  
FROM teachers  
ORDER BY salary DESC;
```
- We can order more than one columns
```
SELECT last_name, school, hire_date  
FROM teachers  
ORDER BY school ASC, hire_date DESC;
```

### Filtering: Subset of rows - WHERE
> Who from X school (Subset)?
- Sometimes, you'll want to limit the rows a query return to only those in which one or more columns meet certain criteria
```
SELECT last_name, school, hire_date  
FROM teachers  
WHERE school = 'Myers Middle School';
```
```
SELECT school  
FROM teachers  
WHERE school <> 'F.D. Roosevelt HS';
```
```
SELECT first_name, last_name, hire_date  
FROM teachers  
WHERE hire_date < '2000-01-01';
```
![[lab1.3.png]]
- Combine operator with `AND` and `OR`
```
SELECT *  
FROM teachers  
WHERE school = 'F.D. Roosevelt HS'  
AND (salary < 38000 OR salary > 40000);
```

> **Try it Yourself**
> 1. The school district superintendent asks for a list of teachers in each school. Write a query that lists the schools in alphabetical order along with teachers ordered by last name A–Z.
> 2. Write a query that finds the one teacher whose first name starts with the letter S and who earns more than $40,000.
> 3. Rank teachers hired since January 1, 2010, ordered by highest paid to lowest.

## 2. Basic Maths and Stats with SQL
### Common Operators
- As mention in [[1-2-DWH & SQL]], each colums will have specific data type, `CAST` to convert `integer` to `character`
- `AS` is used to assign an Alias (New name for the ouput columns)
```
SELECT 
	2 + 2 AS a,
	9-1 AS b,
	3 * 4 AS c,
	11 / 6 AS d,
	11 % 6 AS e,
	CAST(11/6 AS char(10)) AS f,
	3^3 AS g;
```
### Calculate with columns
```
SELECT 
	*,
	distance / duration AS speed
FROM taxi_rides
WHERE distance > 0
```
### Aggregation Functions
- Common Summarize Functions: `COUNT`, `SUM`, `AVG`, `MIN`,`MAX`,`STD`
- All available PostgreSQL Aggregate Function [PostGresql Docs](https://www.postgresql.org/docs/9.1/functions-aggregate.html)
```
SELECT 
	MIN(duration),
	MAX(duration),
	STDDEV(duration)
FROM taxi_rides
```
## 3. GROUP BY and Summarize
### Demo 1: Compare by Vendor
- By combining the `GROUP BY` and Summarize Function, we can uncover interesting patterns in the data table

- In the demo below:
	- Use `GROUP BY`, to split into groups that we want to compare
	- Use aggregate function: `COUNT`, `AVG`
	- Using `WHERE` to select only the valid rides (`distance > 0`)
- We are able to compare 2 groups:
	- *Vendor 2* have more rides, longer duration
	- Though the distance are relatively equivalent between 2 vendors

![[lab1.5.png]]
```
SELECT 
	vendor_id,
	COUNT(id) AS ride_count,
	AVG(passenger_count) AS avg_passenger_count,
	AVG(duration) AS avg_duration,
	AVG(distance) AS avg_distance
FROM taxi_rides
WHERE distance > 0
GROUP BY vendor_id
```
### Demo 2: Compare by Size of Ride
- How we define the size of ride? -> Number of passengers on the ride (more people is bigger size)
- Insights:
	- Most of the ride is for 1 passenger
	- Alone passenger tends to travel shorter duration, and distance
	- Hypothesis: Rides with many passenger (travel together to share cost, family go to airports?)
	- Some data issues: 0 passenger ride (??)

![[lab1.6.png]]
```
SELECT 
	passenger_count,
	COUNT(id) AS ride_count,
	AVG(duration) AS avg_duration,
	AVG(distance) AS avg_distance,
	AVG(duration/distance) AS avg_speed
FROM taxi_rides
WHERE distance > 0
GROUP BY passenger_count
ORDER BY ride_count DESC
```
> **Discussion**: From the insights as above, what actionable recommendations you wil have for the business?

## Recap
When exploring new datasets, there are several things we could do (SQL is a tool for those):
1. Understand the schema: What colums the tables have?
2. Check the granularity? (i.e. ID of the tables)
3. Extract some early insights by:
	- Sorting
	- Filter subset of Data
	- Check the value of important columns:
		- Non-numeric: `DISTINCT`
		- Numeric: `AVG`, `MIN`, `MAX`, `SUM` ([[#Aggregation Functions]])
4. Add more columns that are calculated from exisitng columns ([[#2 Basic Maths and Stats with SQL]])
5. Compare among different subsets of the data: Using `GROUP BY` and Summarize [[#3 GROUP BY and Summarize]]