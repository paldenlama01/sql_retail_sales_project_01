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

## 2. Data Inspection
Basic queries to:
- View sample records using `LIMIT 10`
- Count total rows in the dataset

## 3. Data Cleaning
Identifies and removes rows containing `NULL` values in key fields like `transactions_id`, `sale_date`, `gender`, `category`, `quantiy`, and `total_sale`.  
The cleaning ensures all records are valid before analysis.

## 4. Data Exploration
Basic metrics:
- Total number of sales (`COUNT(*)`)
- Total number of unique customers (`COUNT(DISTINCT customer_id)`)

These give a quick snapshot of dataset size and customer diversity.

## 5. Analysis and Insights
This section answers specific business questions:

### 1. Sales on a Specific Date
Retrieves all sales made on `'2022-11-05'`.

### 2. High-Volume Clothing Sales
Returns all transactions in *Clothing* where quantity ≥ 4 during November 2022.

### 3. Total Sales per Category
Groups data by category and calculates:
- `SUM(total_sale)` as total revenue
- `COUNT(*)` as total orders

### 4. Average Age of Beauty Customers
Calculates mean customer age for those purchasing from the *Beauty* category.

### 5. High-Value Transactions
Lists transactions where `total_sale > 1000`.

### 6. Sales Distribution by Gender and Category
Counts total transactions grouped by gender and category.

### 7. Monthly Average Sales and Best Month per Year
Extracts year and month from `sale_date`, calculates `AVG(total_sale)` for each month, and uses `RANK()` to find the top-performing month in each year by average sale value.

Includes two working versions:
- `TO_CHAR()` formatted output
- `ROUND()` with numeric rounding for numerical precision

### 8. Top 5 Customers by Total Sales
Aggregates total sales per customer and returns the top 5 spenders.

### 9. Unique Customers per Category
Counts distinct customers within each product category.

### 10. Sales by Shift
Creates logical time-based shifts:
- *Morning*: before 12:00  
- *Afternoon*: between 12:00–17:00  
- *Evening*: after 17:00  

Counts total orders in each shift using a `CASE` statement and CTE (`WITH` clause).

## 6. Output Summary
By running all queries, you will obtain:
- Cleaned dataset free from null values.
- Key metrics on customers, categories, and sales.
- Insights on top-performing months, customers, and product categories.
- A time-based breakdown of sales activity by shift.

