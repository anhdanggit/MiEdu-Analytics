---
noteId: "9880446016d911ecb4855f4b7fc4fd1e"
tags: []

---


## 1. SQL on Google BigQuery
### Login Google Cloud Platform
- [**Google BigQuery**](https://cloud.google.com/bigquery) is one of most popular Big Data Analytics Platform nowadays.
- You can use SQL to query and gain insights on BigQuery
> 1. Go to: [cloud.google.com](https://cloud.google.com/)
> 2. Sign in by Your Google Account
> 3. Click `Go to Console`
![[lab2.1.png]]
> 4. On the search bar, enter: `BigQuery`
![[lab2.2.png]]

### BigQuery Public Datasets
- BigQuery provides various public datasets for us to explore
> 1. Click `Add Data` 
> 2. `Explore Public Datasets`
- There are many topics to explore from Analytics, BigData to COVID. 
- You can query and get insights by SQL

![[lab2.3.png]]

### Dataset: hacker_news 
> Hacker New is a social news website focusing on computer science and entrepreneurship: https://news.ycombinator.com/
- To illustrate, on this lab, we will explore the `hacker_news` dataset
- This dataset contains all stories and comments from Hacker News.
- First thing to do is exploring the schema (structure of columns, datatype), description and size of tables

![[lab2.4.png]]
![[lab2.5.png]]

- Check the granularity:
	- Table `stories`: Each row is a story, have unique `id` -> We have `1,959,809` stories
	- Without `AS` you can still asign the alias (temporary name) `s` to table `stories`
	- Call a column belongs to a table `s.<column>`, this is to instruct the machine by SQL language where it can find a column name (to reduce the confusion)
	```
	SELECT
		COUNT(DISTINCT s.id) AS story_cnt,
		COUNT(*) AS row_cnt
	FROM `bigquery-public-data.hacker_news.stories` s
	```
	- Table `comments`: Each row is a story, have unique `id` -> We have `8,399,417` stories
	```
	SELECT
		COUNT(DISTINCT c.id) AS comment_cnt,
		COUNT(*) AS row_cnt
	FROM `bigquery-public-data.hacker_news.comments` c****
	```

## 3. SQL: Join Tables
There are different types of joins (with the SQL codes as in the below charts). But in reality: `INNER JOIN` and `LEFT JOIN` is the most common
![[lab2.7.png]]

### INNER JOIN
- As discussion in [[1-2-DWH & SQL]], tables could be joined by key? 
- How to define keys to join, notice all ID columns:
	- `id`: unique comment ID / unique story ID
	- `parent`: Parent comment ID
- In each table, what is the granularity?
- Then, check if the `parent` is the key that enable us to map `comment` to `story`, we will try this query:
```
SELECT
	COUNT(DISTINCT s.id) AS story_cnt,
	COUNT(DISTINCT c.id) AS comment_cnt
FROM
	`bigquery-public-data.hacker_news.stories` s
INNER JOIN
	`bigquery-public-data.hacker_news.comments` c
ON
	c.parent = s.id
```
![[lab2.6.png]]

> - Notice that we have more `comment_cnt` than `story_cnt`. 1 story might have multiple comments.
> - By `INNER JOIN`, we see that both the numbers of comments and story is lower than the actual size of single table, because we only keep the mappable rows. 
> - This means that some stories do not have a comment (which make sense). For example, only 483k out of 1.9 mil stories have a comments.
> - But some comments do not have a parent? -> WHY?? We need to investigate.

### LEFT JOIN
- When use `LEFT JOIN`, the table on the left will be preserved the same size (numbers of rows), and return `NULL` value for unmapped rows
- Table `stories` on the Left, `story_cnt` stays same to the original table.
```
SELECT
	COUNT(DISTINCT s.id) AS story_cnt,
	COUNT(DISTINCT c.id) AS comment_cnt
FROM
	`bigquery-public-data.hacker_news.stories` s
LEFT JOIN
	`bigquery-public-data.hacker_news.comments` c
ON
	c.parent = s.id
```
![[lab2.8.png]]
- Same to `comments` table, when it's put on the left.
![[lab2.9.png]]
- Using `WHERE` as we learnt in [[1-Lab-SQL-Basic]], we can investigate the cases that the comments are not mapped to any story in `stories` table
```
SELECT
	s.id AS story_id,
	s.title AS story_title,
	c.*
FROM
`bigquery-public-data.hacker_news.comments` c
LEFT JOIN
`bigquery-public-data.hacker_news.stories` s
ON
c.parent = s.id
WHERE s.id IS NULL
```
![[lab2.10.png]]
> But, if we scroll to the end of the output table, we still see the `parent` (ID) is available? Any good reason for it? 
> Maybe the comment is to reply to another comment?

## 4. SubQueries & CTE
> How can we check? If the null story_id in the output could be mapped with other comments parent in the `comments` table?
- **Common Table Expressions** (CTE): Save the output from the previous step to a temporaty table named `comment_null_story`, we do it with the `WITH` clause
- LEFT JOIN `comment_null_story` to `comments` table, to see if we could map any of them to other comments (parents)
```
WITH comment_null_story AS (
	SELECT
		s.id AS story_id,
		s.title AS story_title,
		c.*
	FROM
	`bigquery-public-data.hacker_news.comments` c
	LEFT JOIN
	`bigquery-public-data.hacker_news.stories` s
	ON
		c.parent = s.id
	WHERE s.id IS NULL)  

SELECT
	p.id AS parent_id,
	c.*
FROM comment_null_story c
LEFT JOIN `bigquery-public-data.hacker_news.comments` p
ON c.parent = p.id
```
![[lab2.11.png]]

- **Subqueries**: The idea is the same, but now we remove the `WITH ... AS` part and replace the `comment_null_story` by the whole SQL code inside the parentheses `(`, `)`
```
SELECT
	COUNT(DISTINCT p.id) AS parent_cnt,
	COUNT(DISTINCT c.id) AS comment_cnt,
	COUNT(*) AS row_cnt
FROM
	(SELECT
	s.id AS story_id,
	s.title AS story_title,
	c.*
	FROM
	`bigquery-public-data.hacker_news.comments` c
	LEFT JOIN
	`bigquery-public-data.hacker_news.stories` s
	ON
	c.parent = s.id
	WHERE s.id IS NULL) c
LEFT JOIN `bigquery-public-data.hacker_news.comments` p
ON c.parent = p.id
```
> - In practice, I find CTE is much more straighforward and clean-code than subqueries, this help us to *modularize* our SQL code.
> - **Modularize**: decompose your code into blocks to better implement, and maintain.

- By counting the ID after mapping, we could check the status and under stand the relationship of data -> All 5.7mil comments that are not mappable to story, actually could map with another comments as parent
![[lab2.12.png]]

- By the previous tasks, now we know:
	- Comments could have either story or other comment as parents
	- One parent (comment or story) could have multiple comments
	- Some stories do not have any comments

## 5. Colab Notebook
- Notebook is the most common working environments of Data Analyst and Data Scientist. In the notebook, we can write notes and comments with markdown ([Markdown CheatSheet](https://www.markdownguide.org/cheat-sheet/)), code (Python/SQL) in cells, and the outputs are also displayed.
- It enables us to run code interactively (cell by cell)
![[lab2.13.png]]

> Notebook of this Lab: [Colab-DuckDB-BQ.ipynb](https://colab.research.google.com/drive/1f_51cwJQ_SlQjFGIxTRZ4a43bHMxPb6Y?usp=sharing)

### Create a Colab Notebook
- With your google account, go to: [colab.research.google.com](https://colab.research.google.com/) 
- `Create New Notebook` -> Rename by click to the filename

> Google will generate the folder `Colab Notebooks` on the Google Drive connect to your Google Account.

### Import CSV files to Colab
- On the left-panel, click on the `folder` icon -> `upload` icon
- Upload the `csv` file
![[lab2.15.png]]

### Install DuckDB
- We will install `DuckDB` ([DuckDB Docs](https://duckdb.org/docs/)) which enables us to working with data in notebook by SQL syntax
- The Notebook environment will enable us to work with both SQL and Python syntax seamlessly 
- To install `DuckDB to colab`, run this code in cell:
```
!pip install duckdb
```
### Load Libraries
- In python, you will need to import the library to the working session, to be able to use it:
```
import duckdb as ddb
```
- We will also read the `csv` by `pandas` (the most common Python library for Data Analytics). We do not install because Colab already have the common library (pandas, matplotlib, seaborn, etc.)
```
import pandas as pd
```
### Create the DuckDb Connection
```
con = duckdb.connect(database=':memory:', read_only=False) 
```

### DuckDB: SQL on CSV
`Duckdb` enables you to load `csv` files and query by SQL (without having to create the table and schema as importing to PostGres in [[1-Lab-SQL-Basic]])

```
SQL_script1 ='''
SELECT * FROM 'sample.csv'
LIMIT 100;
'''
con.execute(SQL_script1).fetch_df()
```

### DuckDB: SQL on Pandas Dataframe
- Read `csv` by `pandas`: `sample = pd.read_csv('sample.csv')`
- SQL on Pandas DataFrame
```
SQL_script2 ='''
SELECT
	vendor_id,
	SUM(duration) AS sum_distance
FROM sample
GROUP BY vendor_id
'''
con.execute(SQL_script2).fetch_df()
```

## 6. Colab <> BigQuery
- Google Colab enables us to connect with BigQuery conveniently
- Authorize the connection to your GCP project:
```
from google.colab import auth
auth.authenticate_user()
print('Authenticated')
```
- Start Query
```
%%bigquery --project your-project-id
SELECT
*
FROM `bigquery-public-data.austin_bikeshare.bikeshare_stations`
LIMIT 100
```
- Where to get the project_id:
![[lab2.16.png]]

## Recap
1. Introduce the Google BigQuery - a popular Big Data Analytics Platform nowsaday that we work on SQL to get the insights from data. 
2. Joining information from different tables:
	- With `INNER JOIN` and `LEFT JOIN`, you could work on most of the data tasks
	- Identify granularity 
	- Identify keys to map two tables
	- Investigate the behaviors of keys: when it is mappable, when it is unmappable
	- By that we understand the relationship between keys and 2 tables
3. When the SQL query become more complex, [[#4 SubQueries CTE]] are helpful to modularize your code.
4. All of them are conducted on the foundation of common SQL syntax introduced in [[1-Lab-SQL-Basic]] to explore the dataset `hacker_news` on BigQuery public datasets.
5. Notebook is the most common working environments of Data Analyst and Data Scientist. We introduce Google Colab, which enable us to work with Python without having to set-up, share the notebooks to colab.