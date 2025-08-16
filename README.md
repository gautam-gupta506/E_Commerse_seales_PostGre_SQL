# Retail Sales Analysis SQL Project

## Project Overview

**Project Title**: E_Commerce_Sales_analysis_SQL 
**Level**: Beginner  
**Database**: `e_commerce_data`

This project is designed to demonstrate SQL skills and techniques typically used by data analysts to explore, clean, and analyze retail sales data. The project involves setting up a retail sales database, performing exploratory data analysis (EDA), and answering specific business questions through SQL queries. This project is ideal for those who are starting their journey in data analysis and want to build a solid foundation in SQL.

## Objectives

1. **Set up a E_Commerce_database**: Create and populate a retail sales database with the provided sales data.
2. **Data Cleaning**: Identify and remove any records with missing or null values.
3. **Exploratory Data Analysis (EDA)**: Perform basic exploratory data analysis to understand the dataset.
4. **Business Analysis**: Use SQL to answer specific business questions and derive insights from the sales data.

## Project Structure

### 1. Database Setup

- **Database Creation**: The project starts by creating a database named `e_commerce_data`.
- **Table Creation**: A table named `E_commerce` is created to store the sales data. The table structure includes columns for transaction ID, sale date, sale time, customer ID, gender, age, product category, quantity sold, price per unit, cost of goods sold (COGS), and total sale amount.

```sql
CREATE DATABASE e_commerce_data;

-- Create Table E_commerce selaes.
create table E_commerce(
Transaction_ID int primary key,
User_Name TEXT NOT NULL,
Age int not null,
Country varchar(50) not null,
Product_Category varchar(60) not null,
Purchase_Amount numeric(5,2) not null,
Payment_Method varchar(60) not null,
Transaction_Date date not null);

-- now import dataset from device.

copy E_commerce(Transaction_ID,User_Name, Age, Country,
Product_Category, Purchase_Amount,
Payment_Method, Transaction_Date)
from 'D:/E_Commerce_datasets.csv'
Delimiter ',' CSV header;
```
### 2. Data Exploration & Cleaning

- **check null values**: Determine the total number of records in the dataset.
- **Customer Count**: Find out how many unique customers are in the dataset.
- **Category Count**: Identify all unique product categories in the dataset.
- **Null Value Check**: Check for any null values in the dataset and delete records with missing data.

```sql
--CLEANNING

-- FOR TRANSACTION_ID.
SELECT COUNT(TRANSACTION_ID) FROM E_COMMERCE
WHERE TRANSACTION_ID IS NULL;
--FOR USER_NAME
SELECT COUNT(USER_NAME) FROM E_COMMERCE
WHERE USER_NAME IS NULL;
-- FOR AGE
SELECT COUNT(AGE) FROM E_COMMERCE
WHERE AGE IS NULL;
--FOR Country Product_Category
SELECT COUNT(Product_Category) FROM E_COMMERCE
WHERE Product_Category IS NULL;
-- FOR Purchase_Amount
SELECT COUNT(Purchase_Amount) FROM E_COMMERCE
WHERE Purchase_Amount IS NULL;
-- FOR Payment_Method
SELECT COUNT(Payment_Method) FROM E_COMMERCE
WHERE Payment_Method IS NULL;
-- FOR Transaction_Date.
SELECT COUNT(Transaction_Date) FROM E_COMMERCE
WHERE Transaction_Date IS NULL;

--PREPROSSING
-- EXTRACT DATA FROM TRANSACTION DATE.
SELECT TRANSACTION_DATE FROM E_COMMERCE;

ALTER TABLE E_COMMERCE
DROP COLUMN WEEK_DAY,
DROP COLUMN MONTH_NAME,
DROP COLUMN IF EXISTS YEAR;
-- ADD NEW COLUMNS WEEK_DAY NAE AND MONTH NAME EXTRACT FROM TEANSACTION_DATE.
ALTER TABLE E_COMMERCE
ADD COLUMN YEAR NUMERIC(4),
ADD COLUMN WEEK_DAY VARCHAR(10),
ADD COLUMN MONTH_NAME VARCHAR(10);
--EXTRACT AND FILL THE VALUES IN THE CELL.
UPDATE E_commerce
SET 
    week_day   = TRIM(TO_CHAR(Transaction_Date, 'Day')),
    month_name = TRIM(TO_CHAR(Transaction_Date, 'Month')),
    "year"     = EXTRACT(YEAR FROM Transaction_Date);	
-- CHECK UNIQUE VALUES IN A PARTICULAR COLUMN	
SELECT DISTINCT PAYMENT_METHOD FROM E_COMMERCE;
SELECT DISTINCT COUNTRY FROM E_COMMERCE;
SELECT DISTINCT PRODUCT_CATEGORY FROM E_COMMERCE;
-- CHECK UNIQUE AGE AND ARRANGE ASCINDING ORDER
SELECT DISTINCT AGE FROM E_COMMERCE
ORDER BY AGE ASC;
--ADD NEW COLUNS ON THE AGE GROUP.
ALTER TABLE E_commerce
ADD COLUMN AGE_GROUP VARCHAR(20);
-- ENTER DATA INTO COLUMNS.
UPDATE E_commerce
SET 
	AGE_GROUP = CASE
	WHEN AGE BETWEEN 18 AND 28 THEN 'TEEN_AGERS'
	WHEN AGE>=29 AND AGE<=37 THEN 'ADULTS'
	WHEN AGE>=38 THEN 'SENIOR CITIZENS'
	end;
--DROP COLUMNS.	
ALTER TABLE E_COMMERCE
DROP COLUMN AGE_GROUP;
-- CHECK NULL VALUES
SELECT COUNT(AGE_GROUP) FROM E_commerce
WHERE AGE_GROUP IS NULL;

--DISPLAY THE TABLE
SELECT * FROM E_COMMERCE;

-- ASCIENDING ORDER ON THE BASED ON TRANSACTION_ID
SELECT * FROM E_COMMERCE 
ORDER BY TRANSACTION_ID ASC;
```

### 3. Data Analysis & Findings

The following SQL queries were developed to answer specific business questions:
1. What is the total sales revenue per year?
2. Which are the top 5 products based on sales revenue?
3. What are the top 3 payment methods based on sales revenue?
4. Which are the top 5 countries based on sales revenue?
5. Which are the top 5 product categories based on sales revenue, broken down by country?
6. What is the sales distribution across different customer age groups?
7. How much sales revenue is generated per month?
8. How much sales revenue is generated per day of the week?
9. How many unique customers purchase each year (total customers per year)?
10. Which month has the highest customer visits and sales revenue?
11. Which type of product is purchased the most by customers?

1. What is the total sales revenue per year?
```sql
SELECT SUM(PURCHASE_AMOUNT),year
FROM E_commerce
GROUP BY YEAR;


```
2. Which are the top 5 products based on sales revenue?
```sql
SELECT  PRODUCT_CATEGORY,SUM(PURCHASE_AMOUNT) AS TOTAL_AMOUNT
FROM E_commerce
GROUP BY PRODUCT_CATEGORY 
ORDER BY TOTAL_AMOUNT DESC LIMIT 5;
```
3. What are the top 3 payment methods based on sales revenue?
```sql
SELECT  PAYMENT_METHOD,SUM(PURCHASE_AMOUNT) AS TOTAL_AMOUNT
FROM E_commerce
GROUP BY PAYMENT_METHOD 
ORDER BY TOTAL_AMOUNT DESC LIMIT 3;
```
4. Which are the top 5 countries based on sales revenue?
```sql
SELECT  COUNTRY,SUM(PURCHASE_AMOUNT) AS TOTAL_AMOUNT
FROM E_commerce
GROUP BY COUNTRY 
ORDER BY TOTAL_AMOUNT DESC LIMIT 5;
```
5. Which are the top 5 product categories based on sales revenue, broken down by country?

```sql
SELECT  PRODUCT_CATEGORY,COUNTRY,SUM(PURCHASE_AMOUNT) AS TOTAL_AMOUNT
FROM E_commerce
GROUP BY PRODUCT_CATEGORY, COUNTRY
ORDER BY TOTAL_AMOUNT DESC LIMIT 5;
```
6. What is the sales distribution across different customer age groups?
```sql
SELECT  AGE_GROUP,SUM(PURCHASE_AMOUNT) AS TOTAL_AMOUNT
FROM E_commerce
GROUP BY AGE_GROUP
ORDER BY TOTAL_AMOUNT DESC LIMIT 5;
```
7. How much sales revenue is generated per month?
```sql
SELECT  MONTH_NAME,SUM(PURCHASE_AMOUNT) AS TOTAL_AMOUNT
FROM E_commerce
GROUP BY MONTH_NAME
ORDER BY 
    CASE TRIM(MONTH_NAME)
        WHEN 'January' THEN 1
        WHEN 'February' THEN 2
        WHEN 'March' THEN 3
        WHEN 'April' THEN 4
        WHEN 'May' THEN 5
        WHEN 'June' THEN 6
        WHEN 'July' THEN 7
		WHEN 'August' THEN 8
		WHEN 'September' THEN 9
		WHEN 'October' THEN 10
		WHEN 'November' THEN 11
		WHEN 'Decembe' THEN 12
    END; 
```
8. How much sales revenue is generated per day of the week?
```sql
SELECT  WEEK_DAY,SUM(PURCHASE_AMOUNT) AS TOTAL_AMOUNT
FROM E_commerce
GROUP BY WEEK_DAY
ORDER BY 
    CASE TRIM(week_day)
        WHEN 'Monday' THEN 1
        WHEN 'Tuesday' THEN 2
        WHEN 'Wednesday' THEN 3
        WHEN 'Thursday' THEN 4
        WHEN 'Friday' THEN 5
        WHEN 'Saturday' THEN 6
        WHEN 'Sunday' THEN 7
    END; 
```
9. How many unique customers purchase each year (total customers per year)?
```sql
SELECT YEAR,COUNT(TRANSACTION_ID) AS TOTAL
FROM E_COMMERCE
GROUP BY YEAR;
```
10. Which month has the highest customer visits and sales revenue?
```sql
SELECT MONTH_NAME,COUNT(TRANSACTION_ID) AS TOTAL
FROM E_COMMERCE
GROUP BY MONTH_NAME
ORDER BY 
    CASE TRIM(MONTH_NAME)
        WHEN 'January' THEN 1
        WHEN 'February' THEN 2
        WHEN 'March' THEN 3
        WHEN 'April' THEN 4
        WHEN 'May' THEN 5
        WHEN 'June' THEN 6
        WHEN 'July' THEN 7
		WHEN 'August' THEN 8
		WHEN 'September' THEN 9
		WHEN 'October' THEN 10
		WHEN 'November' THEN 11
		WHEN 'Decembe' THEN 12
    END; 
```
11. Which type of product is purchased the most by customers?
```sql
SELECT PRODUCT_CATEGORY, COUNT(TRANSACTION_ID) AS TOTAL_CUSTOME
FROM E_COMMERCE
GROUP BY PRODUCT_CATEGORY;
```
##Findings
- **Customer Demographics**: SQL queries revealed that customers belong to different age groups, with major sales concentrated in categories like Clothing and Beauty.
- **High-Value Transactions**: By filtering sales amounts, it was observed that multiple transactions exceeded 1000, indicating premium purchases.
- **Sales Trends**: Using date-based queries, monthly sales analysis highlighted peak seasons and declining periods.
- **Customer Insights**: SQL aggregations helped identify top-spending customers and the most purchased product categories.

##Reports Generated

- **Sales Summary**: Query outputs included total sales revenue, average order value, and category-wise sales performance.
- **Trend Analysis**: Time-series SQL queries provided insights into monthly sales, weekly sales distribution, and seasonal patterns.
- **Customer Insights**: Reports included unique customers per year, top buyers, and preferred payment methods.

##Conclusion

This SQL-based project demonstrates how to use queries for data cleaning, aggregation, and business insights. It covers database design, query optimization, and real-world analysis such as sales by year, top products, payment methods, and customer behavior.
The findings provide actionable insights into sales patterns, product performance, and customer demographics, helping businesses make data-driven decisions.



