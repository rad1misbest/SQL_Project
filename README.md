# Retail Sales Analysis SQL Project and visualization in Power BI

## Project Overview

**Project Title**: Retail Sales Analysis  





## Project Structure

### 1. Database Setup

- **Database Creation**: The project starts by creating a database named `retail_sales_project_2`.
- **Table Creation**: A table named `retail_sales` is created to store the sales data. The table structure includes columns for transaction ID, sale date, sale time, customer ID, gender, age, product category, quantity sold, price per unit, cost of goods sold (COGS), and total sale amount.

```sql
CREATE TABLE retail_sales
(
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

### 2. Data Exploration & Cleaning


```sql
SELECT COUNT(*) FROM retail_sales;

SELECT * FROM retail_sales
WHERE transactions_id IS NULL;


SELECT*FROM retail_sales
WHERE sale_date IS NULL;

SELECT*FROM retail_sales
WHERE sale_time IS NULL;

SELECT * FROM retail_sales
	WHERE 
		transactions_id IS NULL
		OR
		sale_time IS NULL
		OR
		sale_date IS NULL
		OR 
		customer_id IS NULL
		OR
		gender IS NULL
		OR
		category IS NULL
		OR 
		quantiy IS NULL
		OR
		price_per_unit IS NULL
		OR
		cogs IS NULL
		OR
		total_sale IS NULL
		OR
		AGE IS NULL;

DELETE FROM retail_sales
WHERE 
		transactions_id IS NULL
		OR
		sale_time IS NULL
		OR
		sale_date IS NULL
		OR 
		customer_id IS NULL
		OR
		gender IS NULL
		OR
		category IS NULL
		OR 
		quantiy IS NULL
		OR
		price_per_unit IS NULL
		OR
		cogs IS NULL
		OR
		total_sale IS NULL
		OR
		AGE IS NULL;
```
```sql
--DATA exploration / zkoumání dat
-- How many sales we have ? / Kolik prodejů máme? 
SELECT COUNT(total_sale) FROM retail_sales;

-- How many unique customers we have ? Kolik unikatních zákazníků máme?
SELECT COUNT (DISTINCT(customer_id)) FROM retail_sales;

--How many unique category we have? Kolik unikatních kategorií máme?
SELECT DISTINCT(category) FROM retail_sales;
```
### 3. Data Analysis & Business key problem & Answers / Analýza dat a hlavní obchodní problémy & odpovědi

TMy Analysis & Findings / Moje analýza a odpovědi:

1. **Write a SQL query to retrieve all columns for sales made on '2022-11-05**:
```sql
SELECT * FROM retail_sales
WHERE sale_date = '2022-11-05';
```

2. **Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 4 in the month of Nov-2022**:
```sql
SELECT category, 
*
FROM retail_sales
WHERE category = 'Clothing'
	AND
	TO_CHAR(sale_date, 'YYYY-MM')='2022-11'
	AND 
	quantiy >=4
```

3. **Write a SQL query to calculate the total sales (total_sale) for each category.**:
```sql
SELECT 
category,SUM(total_sale) AS net_sale,
COUNT (*) AS total_orders
FROM retail_sales
GROUP BY category;
```

4. **Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.**:
```sql
SELECT
category,ROUND(AVG(age),2) AS average_age
FROM retail_sales
GROUP BY category
HAVING category = 'Beauty';
```

5. **Write a SQL query to find all transactions where the total_sale is greater than 1000.**:
```sql
SELECT 
transactions_id,SUM(total_sale) 
FROM retail_sales
GROUP BY transactions_id
HAVING SUM(total_sale) > 1000;
```

6. **Write a SQL query to find the total number of transactions (transaction_id) made by each gender in each category.**:
```sql
SELECT 
category,gender, COUNT(transactions_id)
FROM retail_sales
GROUP BY (gender,category)
```

7. **Write a SQL query to calculate the average sale for each month. Find out best selling month in each year**:
```sql
SELECT year,month,avg_sale FROM
(
SELECT
	EXTRACT (year FROM sale_date) AS year,
	EXTRACT (month FROM sale_date) AS month,
	ROUND(AVG(total_sale)) AS AVG_sale,
	RANK() OVER(PARTITION BY EXTRACT (year FROM sale_date) ORDER BY ROUND(AVG(total_sale))DESC) AS RANK
FROM retail_sales
GROUP BY 1, 2) AS T1
WHERE RANK = 1
```

8. **Write a SQL query to find the top 5 customers based on the highest total sales **:
```sql
SELECT 
customer_id,SUM(total_sale) AS HGT_total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY 2 DESC
LIMIT 5;
```

9. **Write a SQL query to find the number of unique customers who purchased items from each category.**:
```sql
SELECT 
category, COUNT (DISTINCT(customer_id)) AS cnt_unique_customer
FROM retail_sales
GROUP BY category
ORDER BY 2 DESC ;
```

10. **Write a SQL query to create each shift and number of orders (Example Morning <12, Afternoon Between 12 & 17, Evening >17)**:
```sql
WITH hourly_sale
AS
(
SELECT *,
CASE
	WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
	WHEN EXTRACT(HOUR FROM sale_time)BETWEEN 12 AND 17 THEN 'Afternoon'
	ELSE 'Evening'
END as shift

FROM retail_sales
)

SELECT shift, COUNT(*) AS total_orders
FROM hourly_sale
GROUP BY shift;
```


