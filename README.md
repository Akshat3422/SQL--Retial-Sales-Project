-- Data Cleaning
For checking the columns which have  null values in our dataset
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
-- SELECT *
-- FROM retail_sales
-- WHERE age IS NULL
--    OR quantity IS NULL
--    OR price_per_unit IS NULL
--    OR cogs IS NULL
--    OR total_sale IS NULL;

-- Deleting the null values
DELETE
FROM retail_sales
WHERE age IS NULL
   OR quantity IS NULL
   OR price_per_unit IS NULL
   OR cogs IS NULL
   OR total_sale IS NULL;

SELECT COUNT(*)
FROM retail_sales;


-- Data Exploration Step 
-- How many sales do we have?
SELECT COUNT(*) AS 	total_sales
FROM retail_sales;

-- How many unique customers do we have?
SELECT COUNT(DISTINCT(customer_id)) AS total_customers
FROM retail_sales;

-- types of products
SELECT DISTINCT(category) AS types_of_products
FROM retail_sales


-- Data Analysis & Business Key Problems & Answers

-- My Analysis & Findings
-- Q.1 Write a SQL query to retrieve all columns for sales made on '2022-11-05
-- Q.2 Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 10 in the month of Nov-2022
-- Q.3 Write a SQL query to calculate the total sales (total_sale) for each category.
-- Q.4 Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.
-- Q.5 Write a SQL query to find all transactions where the total_sale is greater than 1000.
-- Q.6 Write a SQL query to find the total number of transactions (transaction_id) made by each gender in each category.
-- Q.7 Write a SQL query to calculate the average sale for each month. Find out best selling month in each year
-- Q.8 Write a SQL query to find the top 5 customers based on the highest total sales 
-- Q.9 Write a SQL query to find the number of unique customers who purchased items from each category.
-- Q.10 Write a SQL query to create each shift and number of orders (Example Morning <=12, Afternoon Between 12 & 17, Evening >17)

-- Q.1 Write a SQL query to retrieve all columns for sales made on '2022-11-05
SELECT * 
FROM retail_sales
WHERE sale_date='2022-11-05'


-- Q.2(a) Write a SQL query to retrieve the sum of quantities where the category is 'Clothing' and the quantity sold is more than 10 in the month of Nov-2022

SELECT EXTRACT(YEAR FROM sale_date) AS year,EXTRACT(MONTH FROM sale_date)AS month,category,SUM(quantity) AS quantity_sold
FROM retail_sales 
WHERE category='Clothing'
GROUP BY category ,month,year
HAVING EXTRACT(MONTH FROM sale_date)=11 AND (EXTRACT(YEAR FROM sale_date))=2022 AND SUM(quantity)>10

-- Q.2(b) Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is atleast 4 in the month of Nov-2022
SELECT *
FROM retail_sales
WHERE category='Clothing' AND (EXTRACT (MONTH FROM sale_date))=11 AND (quantity>=4) AND (EXTRACT (YEAR FROM sale_date))=2022

-- Q.3 Write a SQL query to calculate the total sales (total_sale) for each category.
SELECT category,SUM(total_sale) AS sum_of_total_sales 
FROM retail_sales
GROUP BY category;

-- Q.4 Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.
SELECT category,ROUND(AVG(age),2) AS avg_age
FROM retail_sales
GROUP BY category HAVING category='Beauty'
.0
1

-- Q.5 Write a SQL query to find all transactions where the total_sale is greater than 1000.
SELECT *
FROM retail_sales
WHERE total_sale>1000

-- Q.6 Write a SQL query to find the total number of transactions (transaction_id) made by each gender in each category.
SELECT category,gender,COUNT(transactions_id)
FROM retail_sales
GROUP BY gender,category
ORDER BY category

-- Q.7 Write a SQL query to calculate the average sale for each month. Find out best selling month in each year
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

-- Q.8 Write a SQL query to find the top 5 customers based on the highest total sales 
SELECT  customer_id,SUM(total_sale) AS total_sale_by_each_customer
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sale_by_each_customer DESC
LIMIT 5


-- Q.9 Write a SQL query to find the number of unique customers who purchased items from each category.
SELECT category, COUNT(DISTINCT customer_id) AS unique_customers_count
FROM retail_sales
WHERE category IN ('Beauty', 'Clothing', 'Electronics')
GROUP BY category;

-- Q.10 Write a SQL query to create each shift and number of orders (Example Morning <=12, Afternoon Between 12 & 17, Evening >17)
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


-- End of Project



