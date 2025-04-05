# Retail Sales Analysis using SQL
## Project Overview
**Project Title**: Retail Sales Analysis
**Database**: `First_SQL_Project_db`

In this project we analyse the data of Retail Sales. We are doing it using SQL. We have created the table and have copied the data from a CSV file. We explored,cleaned and analyzed the data. We tried to bring out various business insights. 

## Objectives
1. **Set up a retail sales database**: Create and populate a retail sales database with the provided sales data.
2. **Data Cleaning**: Identified and replaced the null vaues either by mean or median.
3. **Exploratory Data Analysis**: Performed basic EDA to understand the dataset.
4. **Business Insights**: We tried to bring hidden information in the data to the fore using SQL queries.

## Project Structure

### 1. Database Setup

- **Database Creation**: The project starts by creating a database named `p1_retail_db`.
- **Table Creation**: A table named `retail_sales` is created to store the sales data. The table structure includes columns for transaction ID, sale date, sale time, customer ID, gender, age, product category, quantity sold, price per unit, cost of goods sold (COGS), and total sale amount.

```sql
CREATE DATABASE First_SQL_Project_db;
USE First_SQL_Project_db;

ALTER Table retail_sales
ADD Constraint PK Primary KEY(transactions_id);

CREATE TABLE retail_sales(
	transactions_id int,	
	sale_date date,	
	sale_time time,	
	customer_id	int,
	gender VARCHAR (15),	
	age	int,
	category VARCHAR (20),	
	quantiy	INT,
	price_per_unit int,	
	cogs float,	
	total_sale float
)
```

```sql
-- No of records in the table. 
SELECT COUNT(*) FROM retail_sales;
-- There is 200 records in the table.

-- Checking all the columns having null values.
SELECT * FROM retail_sales
WHERE 
	transactions_id IS NULL
	OR
	sale_date is NULL
	OR
	sale_time is null
	OR
	customer_id is null
	or
	gender is null
	or
	age is null
	or
	category is null
	or
	quantiy is null
	or
	price_per_unit is null
	or
	cogs is null
	or
	total_sale is null;
```
-- **Data Exploration**
-- Total Purcheses made
```sql
sql SELECT COUNT(*) [Number of purchases made] FROM retail_sales;
```

-- Total number of customers we have
```sql 
SELECT COUNT(Distinct customer_id) [Number of unique buyers] FROM retail_sales;
```
-- Total number of items in each category.
```sql
SELECT category, COUNT(*) [Different categories] FROM retail_sales Group by category;
```

-- **Data Analysis and Key Business Problems and Answers**

-- Q1. Write a SQL Query to retrieve all columns for sale made on '2022-11-05'
```sql
SELECT * FROM retail_sales
	WHERE sale_date = '2022-11-05';
```
-- Q2. Write a SQL Query to retrieve all transactions where the category is 'clothing' and 
 the quantity sold is is more than 10 in the month of novemeber.
```sql
SELECT * 
FROM retail_sales
WHERE 
	category = 'clothing'
	AND quantiy >= 4 
	AND sale_date between '2022-11-01' AND '2022-11-30';
```

--Q3 Find total sales by each category.
```sql
SELECT Category, SUM(total_sale) [Total Sales in each category]
FROM retail_sales
GROUP BY category;
```
--Q4 Find the average age of customers who purchased items from the beauty category.
```sql
SELECT AVG(age) [Average age of customers of Beauty Products]
FROM retail_sales
WHERE Category = 'Beauty';
```

-- Q5 Find all transactions where the total sales is greater than 1000
```sql
SELECT transactions_id as [Transactions valuing more than 1000]
FROM retail_sales
WHERE total_sale > 1000;
```
-- Q6 Find the total number of transactions (transactions id) made by each gender in each category.
```sql
SELECT category, gender, COUNT(transactions_id) [No of purchases be each gender in each category]
FROM retail_sales
GROUP BY category, Gender
ORDER BY category;
```

--7 Find the average sale for each month.
```sql
SELECT
	YEAR(sale_date) as [Year],
	MONTH(sale_date) as [Month],
	ROUND(AVG(total_sale),2) [Avg sales in each month]
FROM retail_sales
GROUP BY Year(sale_date),Month(sale_date)
ORDER BY 1,2;
```
--8 Find out best selling month in each year.
```sql
SELECT Month as [Best selling month], avg_sale  FROM
(
SELECT
	YEAR(sale_date) as year,
	Month(sale_date) as month,
	ROUND(AVG(total_sale),3) as avg_sale,
	RANK() OVER(PARTITION BY YEAR(Sale_date) ORDER BY AVG(Total_sale) DESC ) rank
FROM retail_sales
GROUP BY YEAR(Sale_date), Month(Sale_date)
) AS t1
WHERE rank = 1 -- to get the best selling month
--ORDER BY 1,4; window function already ordered by asc rank
WITH SalesWithRank AS (
    SELECT
        YEAR(sale_date) as [Year],
        MONTH(sale_date) as [Month],
        ROUND(AVG(total_sale), 2) AS [Avg sales in each month],
        RANK() OVER(PARTITION BY YEAR(sale_date) ORDER BY AVG(total_sale) DESC) AS rank
    FROM retail_sales
    GROUP BY YEAR(sale_date), MONTH(sale_date)
)

SELECT 
    [Year],
    [Month],
    [Avg sales in each month],
    CASE WHEN rank = 1 THEN 'Yes' ELSE 'No' END AS [Best Selling Month]
FROM SalesWithRank
ORDER BY [Year], Rank;
```

-- Q8 Find the top 5 customers based on the highest total sales.
```sql
SELECT 
	TOP 5
	customer_id,
	SUM(total_sale) as total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY 2 DESC;
```

-- Q9 Write a sql query to find the number of unique customer who purchased from each category.
```sql
SELECT 
	Category, 
	COUNT(DISTINCT customer_id) AS [Unique Customer in this category]
FROM retail_sales 
GROUP BY Category;
```
-- Q10 Write a SQL Query to create each shift and number of orders (Example: Morning <= 12, Afternoon btw 12 and 17 and evening >=17.
```sql
SELECT 
	CASE
		WHEN DATEPART(HOUR, sale_time) < 12 THEN 'Morning'
		WHEN DATEPART(HOUR, sale_time) BETWEEN 12 and 17 THEN  'Afternoon'
		ELSE 'Evening' 
	END AS SHIFT,
		COUNT(*) AS Number_of_orders
FROM  retail_sales
GROUP BY 
	CASE
		WHEN DATEPART(HOUR, sale_time) < 12 THEN 'Morning'
		WHEN DATEPART(HOUR, sale_time) BETWEEN 12 and 17 THEN  'Afternoon'
		ELSE 'Evening'
	END;
```
