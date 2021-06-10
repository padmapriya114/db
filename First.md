# Query
### CREATE
##### syntax

  ```
  CREATE DATABASE databasename;
  ```
###### Example1
  ```
  CREATE TABLE sales.stores (
	store_id INT IDENTITY (1, 1) PRIMARY KEY,
	store_name VARCHAR (255) NOT NULL,
	phone VARCHAR (25),
	email VARCHAR (255),
	street VARCHAR (255),
	city VARCHAR (255),
	state VARCHAR (10),
	zip_code VARCHAR (5)
);
```
######  Example2
```
CREATE TABLE sales.staffs (
	staff_id INT IDENTITY (1, 1) PRIMARY KEY,
	first_name VARCHAR (50) NOT NULL,
	last_name VARCHAR (50) NOT NULL,
	email VARCHAR (255) NOT NULL UNIQUE,
	phone VARCHAR (25),
	active tinyint NOT NULL,
	store_id INT NOT NULL,
	manager_id INT,
	FOREIGN KEY (store_id)
        REFERENCES sales.stores (store_id)
        ON DELETE CASCADE ON UPDATE CASCADE,
	FOREIGN KEY (manager_id)
        REFERENCES sales.staffs (staff_id)
        ON DELETE NO ACTION ON UPDATE NO ACTION
);
```
---
### WHERE  <br>
If you had a table with a hundred million rows of data, reading through all the rows would be inefficient and perhaps even impossible.<br>
In order to filter certain results from being returned, we need to use a WHERE clause in the query
##### SYNTAX
```
Select query with constraints
SELECT column, another_column, …
FROM mytable
WHERE condition
    AND/OR another_condition
    AND/OR …;
```
#### WHERE-BETWEEN
```
SELECT column_name(s)
FROM table_name
WHERE column_name BETWEEN value1 AND value2;
```
### QUERIES WITH CONSTARINST<br>
 When writing WHERE clause with columns containing text data,Sql supports a number of usefull operators.<br>
###### Example
```
name="abc"
name!="abc"
name LIKE ="abc"
name NOT LIKE ="abc"
name LIKE "%AN"(ANd,darAN)
name LIKE "AN_"(ANd)
name IN ("A","B","c")
name NOT IN ("D","E","F")
```
---
### DISTINCT<br>
Even though the data in a database may be unique, the results of any particular query may not be – take our Movies table for example, many different movies can be released the same year. In such cases, SQL provides a convenient way to discard rows that have a duplicate column value by using the DISTINCT keyword.<br>
Since the DISTINCT keyword will blindly remove duplicate rows, we will learn in a future lesson how to discard duplicates based on specific columns using grouping and the GROUP BY clause.<br>
##### SYNTAX
```
Select query with unique results
SELECT DISTINCT column, another_column, …
FROM mytable
WHERE condition(s);
```
---
### ORDER BY<br>
SQL provides a way to sort your results by a given column in ascending or descending order using the ORDER BY clause.<br>
```
Select query with ordered results
SELECT column, another_column, …
FROM mytable
WHERE condition(s)
ORDER BY column ASC/DESC;
```
###### EXAMPLE<br>
1.SELECT DISTINCT Director from Movies Order by Director ASC;<br>
2.SELECT title, year FROM movies
ORDER BY year DESC
LIMIT 4;<br>

---
### LIMITING AND OFFSET<br>
Another clause which is commonly used with the ORDER BY clause are the LIMIT and OFFSET clauses, which are a useful optimization to indicate to the database the subset of the results you care about.<br>
The LIMIT will reduce the number of rows to return, and the optional OFFSET will specify where to begin counting the number rows from.<br>

```
SELECT column, another_column, …
FROM mytable
WHERE condition(s)
ORDER BY column ASC/DESC
LIMIT num_limit OFFSET num_offset;
```
##### EXAMPLE<br>
```
1.SELECT title FROM movies
ORDER BY title ASC
LIMIT 5 OFFSET 5;<br>
```
---

### SELECT

##### SYNTAX
```
1.SELECT * FROM table_name;

2.SELECT query
SELECT column, another_column, …
FROM mytable
WHERE condition(s)
ORDER BY column ASC/DESC
LIMIT num_limit OFFSET num_offset;
```
###### EXAMPLE<br>
1.List all the Canadian cities and their populations
```
SELECT city, population FROM north_american_cities where country="Canada";
```
2.Order all the cities in the United States by their latitude from north to south
```
SELECT city, latitude FROM north_american_cities
WHERE country = "United States"
ORDER BY latitude DESC;
```
3.List all the cities west of Chicago, ordered from west to east
```
SELECT city, longitude FROM north_american_cities
WHERE longitude < -87.629798
ORDER BY longitude ASC;
```
4.List the two largest cities in Mexico (by population
  ```
SELECT city, population FROM north_american_cities
WHERE country LIKE "Mexico"
ORDER BY population DESC
LIMIT 2;
```
5.List the third and fourth largest cities (by population) in the United States and their population
```
SELECT city, population FROM north_american_cities
WHERE country LIKE "United States"
ORDER BY population DESC
LIMIT 2 OFFSET 2;
```
---
### Multi-table queries with JOINs
 we've been working with a single table, but entity data in the real world is often broken down into pieces and stored across multiple orthogonal tables using a process known as normalization<br>

#### DATABASE NORMALIZATION
Database normalization is useful because it minimizes duplicate data in any single table, and allows for data in the database to grow independently of each other<br>

#### PRIMARY KEY
Tables that share information about a single entity need to have a primary key that identifies the entity uniquely across the database..<br>

### JOIN
we can combine row data across two seperate tables using the unique key.<br>

### INNER JOINs
The INNER JOIN is a process that matches rows from the first table and the second table which have the same key (as defined by the ON constraint) to create a result row with the combined columns from both tables. After the tables are joined, the other clauses we learned previously are then applied.<br>
##### SYNTAX
```
SELECT column, another_table_column, …
FROM mytable
INNER JOIN another_table
    ON mytable.id = another_table.id
WHERE condition(s)
ORDER BY column, … ASC/DESC
LIMIT num_limit OFFSET num_offset;

```
###### Example

1.Find the domestic and international sales for each movie
```
SELECT title, domestic_sales, international_sales
FROM movies
  JOIN boxoffice
    ON movies.id = boxoffice.movie_id;
```

2.Show the sales numbers for each movie that did better internationally rather than domestically
```
SELECT title, domestic_sales, international_sales
FROM movies
  JOIN boxoffice
    ON movies.id = boxoffice.movie_id
WHERE international_sales > domestic_sales;
```

3.List all the movies by their ratings in descending order

```
SELECT title, rating
FROM movies
  JOIN boxoffice
    ON movies.id = boxoffice.movie_id
ORDER BY rating DESC;
```    
---

### OUTER JOINs
If the two tables have asymmetric data, which can easily happen when data is entered in different stages, then we would have to use a LEFT JOIN, RIGHT JOIN or FULL JOIN instead to ensure that the data you need is not left out of the results.<br>
##### SYNTAX
```
SELECT column, another_column, …
FROM mytable
INNER/LEFT/RIGHT/FULL JOIN another_table
    ON mytable.id = another_table.matching_id
WHERE condition(s)
ORDER BY column, … ASC/DESC
LIMIT num_limit OFFSET num_offset;
```
---
### NULL
An alternative to NULL values in your database is to have data-type appropriate default values, like 0 for numerical data, empty strings for text data, etc. But if your database needs to store incomplete data, then NULL values can be appropriate if the default values will skew later analysis (for example, when taking averages of numerical data).<br>
### NOTNULL
Sometimes, it's also not possible to avoid NULL values, as we saw in the last lesson when outer-joining two tables with asymmetric data. In these cases, you can test a column for NULL values in a WHERE clause by using either the IS NULL or IS NOT NULL constraint.<br>

##### SYNTAX
```
SELECT column, another_column, …
FROM mytable
WHERE column IS/IS NOT NULL
AND/OR another_condition
AND/OR …;
```
---
### QUERIES WITH EXPRESSION
The use of expressions can save time and extra post-processing of the result data, but can also make the query harder to read, so we recommend that when expressions are used in the SELECT part of the query, that they are also given a descriptive alias using the AS keyword.<br>
##### SYNTAX
```
SELECT col_expression AS expr_description, …
FROM mytable;
```
---
### QUERIES WITH AGGREGATES
SQL also supports the use of aggregate expressions (or functions) that allow you to summarize information about a group of rows of data.<br>
##### SYNTAX

```
SELECT AGG_FUNC(column_or_expression) AS aggregate_description, …
FROM mytable
WHERE constraint_expression;
```

*COUNT(*), COUNT(column) 	A common function used to counts the number of rows in the group if no column name is specified. Otherwise, count the number  of rows in the group with non-NULL values in the specified column.<br>
*MIN(column) 	Finds the smallest numerical value in the specified column for all rows in the group.<br>
*MAX(column) 	Finds the largest numerical value in the specified column for all rows in the group.<br>
*AVG(column) 	Finds the average numerical value in the specified column for all rows in the group.<br>
*SUM(column) 	Finds the sum of all numerical values in the specified column for the rows in the group.<br>
### GROUP BY
The GROUP BY clause works by grouping rows that have the same value in the column specified.<br>

```
SELECT AGG_FUNC(column_or_expression) AS aggregate_description, …
FROM mytable
WHERE constraint_expression
GROUP BY column;
```
###### EXAMPLE
1.Find the longest time that an employee has been at the studio
```
SELECT MAX(years_employed) as Max_years_employed
FROM employees;
```
2.For each role, find the average number of years employed by employees in that role
```
SELECT role, AVG(years_employed) as Average_years_employed
FROM employees
GROUP BY role;
```
3.Find the total number of employee years worked in each building
```
SELECT building, SUM(years_employed) as Total_years_employed
FROM employees
GROUP BY building;
```
#### GROUP BY-HAVING
SQL allows us to do this by adding an additional HAVING clause which is used specifically with the GROUP BY clause to allow us to filter grouped rows from the result set.<br>
##### SYNTAX
```
SELECT group_by_column, AGG_FUNC(column_expression) AS aggregate_result_alias, …
FROM mytable
WHERE condition
GROUP BY column
HAVING group_condition;
```
The HAVING clause constraints are written the same way as the WHERE clause constraints, and are applied to the grouped rows. With our examples, this might not seem like a particularly useful construct, but if you imagine data with millions of rows with different properties, being able to apply additional constraints is often necessary to quickly make sense of the data.<br>
###### EXAMPLE
1.Find the number of Artists in the studio (without a HAVING clause)
```
SELECT role, COUNT(*) as Number_of_artists
FROM employees
WHERE role = "Artist";
```
2.Find the number of Employees of each role in the studio
```
SELECT role, COUNT(*)
FROM employees
GROUP BY role;
```
3.Find the total number of years employed by all Engineers
```
SELECT role, SUM(years_employed)
FROM employees
GROUP BY role
HAVING role = "Engineer";
```
---
