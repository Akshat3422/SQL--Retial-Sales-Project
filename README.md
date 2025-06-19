 
# Retail Sales Analysis SQL Project

## Project Overview

**Project Title**: Retail Sales Analysis  
**Level**: Beginner  
**Database**: `project_1`

This project demonstrates foundational SQL skills used by data analysts to explore, clean, and analyze retail sales data. It includes setting up a retail database, performing EDA, and answering business questions using SQL queries.

---

## Objectives

1. **Database Setup**: Create and populate a retail sales database.
2. **Data Cleaning**: Identify and handle missing/null values.
3. **Exploratory Data Analysis (EDA)**: Gain insights into data structure and content.
4. **Business Analysis**: Answer practical business queries using SQL.

---

## Project Structure

###  1. Database Setup

```sql
CREATE DATABASE project_1;

CREATE TABLE retail_sales (
    transactions_id INT PRIMARY KEY,
    sale_date DATE,	
    sale_time TIME,
    customer_id INT,	
    gender VARCHAR(10),
    age INT,
    category VARCHAR(35),
    quantity INT,
    price_per_unit FLOAT,	
    cogs FLOAT,
    total_sale FLOAT
);
```

---

###  2. Data Exploration & Cleaning

```sql
-- Record Count
SELECT COUNT(*) FROM retail_sales;

-- Unique Customers
SELECT COUNT(DISTINCT customer_id) FROM retail_sales;

-- Product Categories
SELECT DISTINCT category FROM retail_sales;

-- Identify Nulls
-- For checking the columns which have  null values in our dataset
SELECT 
  SUM(CASE WHEN transactions_id IS NULL THEN 1 ELSE 0 END) AS null_transaction_id,
  SUM(CASE WHEN sale_date IS NULL THEN 1 ELSE 0 END) AS null_sale_date,
  SUM(CASE WHEN sale_time IS NULL THEN 1 ELSE 0 END) AS null_sale_time,
  SUM(CASE WHEN customer_id IS NULL THEN 1 ELSE 0 END) AS null_customer_id,
  SUM(CASE WHEN gender IS NULL THEN 1 ELSE 0 END) AS null_customer_gender,
  SUM(CASE WHEN age IS NULL THEN 1 ELSE 0 END) AS null_age,
  SUM(CASE WHEN category IS NULL THEN 1 ELSE 0 END) AS null_category,
  SUM(CASE WHEN quantity IS NULL THEN 1 ELSE 0 END) AS null_quantity,
  SUM(CASE WHEN price_per_unit IS NULL THEN 1 ELSE 0 END) AS null_price_per,
  SUM(CASE WHEN cogs IS NULL THEN 1 ELSE 0 END) AS null_cogs,
  SUM(CASE WHEN total_sale IS NULL THEN 1 ELSE 0 END) AS null_total_sale
FROM retail_sales;

-- -- NOW VIEWING THE NULL VALUES
SELECT *
FROM retail_sales
WHERE age IS NULL
   OR quantity IS NULL
   OR price_per_unit IS NULL
   OR cogs IS NULL
   OR total_sale IS NULL;

-- Deleting the null values
DELETE
FROM retail_sales
WHERE age IS NULL
   OR quantity IS NULL
   OR price_per_unit IS NULL
   OR cogs IS NULL
   OR total_sale IS NULL;


-- Delete Null Records
DELETE FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;
```

---

###  3. Data Analysis & Business Queries

#### Q1. Sales on a Specific Date
```sql
SELECT * FROM retail_sales WHERE sale_date = '2022-11-05';
```

#### Q2. Clothing Sales in Nov-2022 (quantity ≥ 4)
#### Q.2(a) Write a SQL query to retrieve the sum of quantities where the category is 'Clothing' and the quantity sold is more than 10 in the month of Nov-2022
```sql
SELECT EXTRACT(YEAR FROM sale_date) AS year,EXTRACT(MONTH FROM sale_date)AS month,category,SUM(quantity) AS quantity_sold
FROM retail_sales 
WHERE category='Clothing'
GROUP BY category ,month,year
HAVING EXTRACT(MONTH FROM sale_date)=11 AND (EXTRACT(YEAR FROM sale_date))=2022 AND SUM(quantity)>10
```

#### Q.2(b) Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is atleast 4 in the month of Nov-2022
```sql
SELECT *
FROM retail_sales
WHERE category='Clothing' AND (EXTRACT (MONTH FROM sale_date))=11 AND (quantity>=4) AND (EXTRACT (YEAR FROM sale_date))=2022
```
#### Q3. Total Sales by Category
```sql
SELECT category,SUM(total_sale) AS sum_of_total_sales 
FROM retail_sales
GROUP BY category;
```

#### Q4. Average Age of Beauty Category Buyers
```sql
SELECT category,ROUND(AVG(age),2) AS avg_age
FROM retail_sales
GROUP BY category HAVING category='Beauty';

```

#### Q5. High Value Transactions (total_sale > 1000)
```sql
SELECT *
FROM retail_sales
WHERE total_sale>100;
```

#### Q6. Total Transactions by Gender and Category
```sql
SELECT category,gender,COUNT(transactions_id)
FROM retail_sales
GROUP BY gender,category
ORDER BY category
```

#### Q7. Best Selling Month in Each Year
```sql
WITH monthly_sales AS (
  SELECT 
    EXTRACT(YEAR FROM sale_date) AS year,
    EXTRACT(MONTH FROM sale_date) AS month,
    AVG(total_sale) AS total_monthly_sale
  FROM retail_sales
  GROUP BY year, month
),
ranked_months AS (
  SELECT *,
         RANK() OVER (PARTITION BY year ORDER BY total_monthly_sale DESC) AS rank
  FROM monthly_sales
)
SELECT year, month, ROUND(total_monthly_sale::numeric,2)
FROM ranked_months
WHERE rank = 1
ORDER BY year; 

```

#### Q8. Top 5 Customers by Total Sales
```sql
SELECT  customer_id,SUM(total_sale) AS total_sale_by_each_customer
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sale_by_each_customer DESC
LIMIT 5
```

#### Q9. Unique Customers per Category
```sql
SELECT category, COUNT(DISTINCT customer_id) AS unique_customers_count
FROM retail_sales
WHERE category IN ('Beauty', 'Clothing', 'Electronics')
GROUP BY category;
```

#### Q10. Orders by Shift (Morning, Afternoon, Evening)
```sql
SELECT 
CASE 
WHEN EXTRACT(HOUR FROM sale_time)<=12 THEN 'Morning'
WHEN EXTRACT(HOUR FROM sale_time) > 12 AND EXTRACT(HOUR FROM sale_time) <= 17 THEN 'Afternoon'
WHEN 17<EXTRACT(HOUR FROM sale_time) THEN 'Evening'
END AS shift_name,
COUNT(*) AS number_of_orders
FROM retail_sales
GROUP BY shift_name
ORDER by shift_name;

```

---

## Findings

- Customers span various demographics and product interests.
- Multiple high-value transactions hint at premium products.
- Clear sales seasonality patterns are observable.
- Most popular categories and top-spending customers are identifiable.

---

## Reports

- **Sales Summary**: Sales per category and customer insights.
- **Trends**: Monthly trends and time-of-day sales distribution.
- **Customer Analysis**: Unique buyers, repeat customers, and volume.

---

## Conclusion

This beginner-friendly SQL project showcases key database operations, EDA, and practical query writing. It offers insights into business metrics that are essential for decision making and helps lay a foundation for real-world data analyst projects.

---

## Tech Stack

- **SQL**: PostgreSQL
- **Tools**: DBMS like pgAdmin, DBeaver, or any SQL IDE

---

##  Author

**Retail Sales Analysis SQL Project**  
By: Akshat Gupta   

