# Retail Sales SQL Analysis Project 

## Overview
This SQL script analyzes retail sales data to answer key business questions. It includes data setup, cleaning, exploration, and analysis. The goal is to understand sales trends, customer behavior, and category performance.

## 1. Table Creation
The script first drops any existing `retail_sales` table and recreates it with the following fields:
- `transactions_id`: Unique transaction identifier (Primary Key)
- `sale_date`: Date of sale
- `sale_time`: Time of sale
- `customer_id`: Unique customer ID
- `gender`: Customer gender
- `age`: Customer age
- `category`: Product category
- `quantiy`: Number of items sold
- `price_per_unit`: Price per unit item
- `cogs`: Cost of goods sold
- `total_sale`: Total sale amount

This table is designed for transactional-level sales data.
```sql
DROP TABLE IF EXISTS retail_sales;
CREATE TABLE retail_sales
             (
                 transactions_id INT PRIMARY KEY,
				 sale_date DATE,
				 sale_time TIME,	
				 customer_id INT,	
				 gender VARCHAR(15),
				 age INT,	
				 category VARCHAR(15),	
				 quantiy INT,	
				 price_per_unit FLOAT,	
				 cogs FLOAT,	
				 total_sale FLOAT
);
```
## 2. Data Inspection
Basic queries to:
- View sample records using `LIMIT 10`
- Count total rows in the dataset
```sql
SELECT * FROM retail_sales
LIMIT 10
```

## 3. Data Cleaning
Identifies and removes rows containing `NULL` values in key fields like `transactions_id`, `sale_date`, `gender`, `category`, `quantiy`, and `total_sale`.  
The cleaning ensures all records are valid before analysis.
```sql
SELECT * FROM retail_sales
WHERE 
     transactions_id IS NULL
	 OR
	 sale_date IS NULL
	 OR
	 sale_time IS NULL
	 OR 
	 gender IS NULL
	 OR 
	 category IS NULL
	 OR 
	 quantiy IS NULL
	 OR 
	 total_sale IS NULL;

--DELETE NULL Values
DELETE FROM retail_sales
WHERE 
     transactions_id IS NULL
	 OR
	 sale_date IS NULL
	 OR
	 sale_time IS NULL
	 OR 
	 gender IS NULL
	 OR 
	 category IS NULL
	 OR 
	 quantiy IS NULL
	 OR 
	 total_sale IS NULL;

```

## 4. Data Exploration
Basic metrics:
- Total number of sales (`COUNT(*)`)
- Total number of unique customers (`COUNT(DISTINCT customer_id)`)
These give a quick snapshot of dataset size and customer diversity.
```sql
-- How many sales we have? 
SELECT
      COUNT(*) as total_sale
FROM retail_sales

-- How many unique customers we have?
SELECT
      COUNT(DISTINCT customer_id) as total_sale
FROM retail_sales
```

## 5. Analysis and Insights
This section answers specific business questions:

### 1. Sales on a Specific Date
Retrieves all sales made on `'2022-11-05'`.
```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```

### 2. High-Volume Clothing Sales
Returns all transactions in *Clothing* where quantity ≥ 4 during November 2022.
```sql
SELECT 
     *
FROM retail_sales
WHERE category = 'Clothing'
     AND 
	 TO_CHAR(sale_date,'YYYY-MM') = '2022-11'
	 AND 
	 quantiy >= 4
```

### 3. Total Sales per Category
Groups data by category and calculates:
- `SUM(total_sale)` as total revenue
- `COUNT(*)` as total orders
```sql
SELECT 
      category,
	  SUM(total_sale) as net_sale,
	  COUNT(*) AS total_orders
FROM retail_sales
GROUP BY category;
```

### 4. Average Age of Beauty Customers
Calculates mean customer age for those purchasing from the *Beauty* category.
```sql
SELECT 
      ROUND(AVG(age),2) as avg_age
FROM retail_sales
WHERE category = 'Beauty'
```

### 5. High-Value Transactions
Lists transactions where `total_sale > 1000`.
```sql
SELECT * FROM retail_sales
WHERE total_sale > 1000
```

### 6. Sales Distribution by Gender and Category
Counts total transactions grouped by gender and category.
```sql
SELECT 
      category,
	  gender,
	  COUNT(*) as total_rows
FROM retail_sales
GROUP 
     BY 
	 category,
	 gender
```

### 7. Monthly Average Sales and Best Month per Year
Extracts year and month from `sale_date`, calculates `AVG(total_sale)` for each month, and uses `RANK()` to find the top-performing month in each year by average sale value.
Includes two working versions:
- `TO_CHAR()` formatted output
- `ROUND()` with numeric rounding for numerical precision
```sql
SELECT
      year,
	  month,
	  avg_sale
FROM 
(
SELECT 
       EXTRACT(YEAR FROM sale_date) as year,
	   EXTRACT(MONTH FROM sale_date) as month,
	   TO_CHAR(AVG(total_sale), 'FM999999.00') as avg_sale,
	   RANK() OVER(PARTITION BY EXTRACT(YEAR FROM sale_date) ORDER BY AVG(total_sale) DESC) as rank
FROM retail_sales
GROUP BY 1, 2
) as t2
WHERE rank = 1
```

### 8. Top 5 Customers by Total Sales
Aggregates total sales per customer and returns the top 5 spenders.
```sql

SELECT 
      customer_id,
	  SUM(total_sale) as total_sales
	  FROM retail_sales
GROUP BY 1 
ORDER BY 2 DESC
LIMIT 5
```

### 9. Unique Customers per Category
Counts distinct customers within each product category.
```sql

SELECT 
      category,
	  COUNT(DISTINCT customer_id) as cnt_unique_cs
FROM retail_sales
GROUP BY category
```

### 10. Sales by Shift
Creates logical time-based shifts:
- *Morning*: before 12:00  
- *Afternoon*: between 12:00–17:00  
- *Evening*: after 17:00  

Counts total orders in each shift using a `CASE` statement and CTE (`WITH` clause).
```sql
WITH hourly_sale
AS 
(
SELECT *,
     CASE
	      WHEN EXTRACT(HOUR FROM sale_time) < 12  THEN 'Morning'
		  WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17  THEN 'Afternoon'
		  ELSE 'Evening' 
	 END as shift  	 
FROM retail_sales
)
SELECT 
      shift,
	  COUNT(*) as total_orders
FROM hourly_sale
GROUP BY shift
```

## 6. Output Summary
By running all queries, you will obtain:
- Cleaned dataset free from null values.
- Key metrics on customers, categories, and sales.
- Insights on top-performing months, customers, and product categories.
- A time-based breakdown of sales activity by shift.

