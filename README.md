
# Retail Sales Analysis SQL Project

## Project Overview

**Project Title**: Retail Sales Analysis  
**Level**: Beginner  
**Database**: `p1_retail_db`

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
```sql
SELECT * FROM retail_sales
WHERE category = 'Clothing'
  AND TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
  AND quantity >= 4;
```

#### Q3. Total Sales by Category
```sql
SELECT category, SUM(total_sale) AS net_sale, COUNT(*) AS total_orders
FROM retail_sales
GROUP BY category;
```

#### Q4. Average Age of Beauty Category Buyers
```sql
SELECT ROUND(AVG(age), 2) AS avg_age
FROM retail_sales
WHERE category = 'Beauty';
```

#### Q5. High Value Transactions (total_sale > 1000)
```sql
SELECT * FROM retail_sales WHERE total_sale > 1000;
```

#### Q6. Total Transactions by Gender and Category
```sql
SELECT category, gender, COUNT(*) AS total_trans
FROM retail_sales
GROUP BY category, gender
ORDER BY category;
```

#### Q7. Best Selling Month in Each Year
```sql
SELECT year, month, avg_sale
FROM (
  SELECT 
    EXTRACT(YEAR FROM sale_date) AS year,
    EXTRACT(MONTH FROM sale_date) AS month,
    AVG(total_sale) AS avg_sale,
    RANK() OVER (PARTITION BY EXTRACT(YEAR FROM sale_date) ORDER BY AVG(total_sale) DESC) AS rank
  FROM retail_sales
  GROUP BY year, month
) t1
WHERE rank = 1;
```

#### Q8. Top 5 Customers by Total Sales
```sql
SELECT customer_id, SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sales DESC
LIMIT 5;
```

#### Q9. Unique Customers per Category
```sql
SELECT category, COUNT(DISTINCT customer_id) AS cnt_unique_cs
FROM retail_sales
GROUP BY category;
```

#### Q10. Orders by Shift (Morning, Afternoon, Evening)
```sql
WITH hourly_sale AS (
  SELECT *, 
    CASE
      WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
      WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
      ELSE 'Evening'
    END AS shift
  FROM retail_sales
)
SELECT shift, COUNT(*) AS total_orders
FROM hourly_sale
GROUP BY shift;
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

