# Retail Sales Analysis using SQL

## Project Overview

This project demonstrates end-to-end retail sales analysis using **MySQL**. The objective is to transform raw transactional data into meaningful business insights through data cleaning, exploratory data analysis (EDA), and business-driven SQL queries.

The project simulates how SQL is used by retail organizations to monitor sales performance, understand customer purchasing behavior, identify high-value customers, and support data-driven decision-making.

---

# Business Objectives

The primary objectives of this analysis are to:

* Analyze overall sales performance across different product categories.
* Understand customer purchasing behavior using demographic information.
* Identify high-value customers and purchasing trends.
* Discover seasonal sales patterns for better demand forecasting.
* Analyze shopping activity across different times of the day.
* Generate actionable insights to support inventory planning, marketing strategies, and business growth.

---

# Dataset Information

The dataset contains retail transaction records with information related to:

* Transaction ID
* Sale Date
* Sale Time
* Customer ID
* Gender
* Age
* Product Category
* Quantity Purchased
* Price per Unit
* Cost of Goods Sold (COGS)
* Total Sale Amount

Each record represents a single customer transaction.

---

# Database Setup

## Create Database

```sql
CREATE DATABASE RetailSalesAnalysis_utf;
USE RetailSalesAnalysis_utf;
```

## Create Table

```sql
CREATE TABLE retail_sales(
    transactions_id INT PRIMARY KEY,
    sale_date DATE,
    sale_time TIME,
    customer_id INT NULL,
    gender VARCHAR(15),
    age INT NULL,
    category VARCHAR(20),
    quantiy INT NULL,
    price_per_unit FLOAT NULL,
    cogs FLOAT NULL,
    total_sale FLOAT NULL
);
```

---

# Data Cleaning

## Business Significance

Accurate business decisions depend on clean and reliable data. Missing values can lead to incorrect revenue calculations, inaccurate customer analysis, and misleading reports. Therefore, the dataset was validated and incomplete records were removed before performing any analysis.

### Check for Missing Values

```sql
SELECT *
FROM retail_sales
WHERE age IS NULL
   OR quantiy IS NULL
   OR price_per_unit IS NULL
   OR cogs IS NULL
   OR total_sale IS NULL
   OR sale_time IS NULL;
```

### Remove Missing Records

```sql
DELETE FROM retail_sales
WHERE age IS NULL
   OR quantiy IS NULL
   OR price_per_unit IS NULL
   OR cogs IS NULL
   OR total_sale IS NULL
   OR sale_time IS NULL;
```

---

# Exploratory Data Analysis (EDA)

## Business Significance

Before solving business problems, it is important to understand the structure and quality of the dataset. Exploratory analysis provides an overview of the available data and helps validate whether it is suitable for further analysis.

### Total Number of Transactions

```sql
SELECT COUNT(*) AS Total_Transactions
FROM retail_sales;
```

Additional exploration included:

* Product category distribution
* Customer demographic overview
* Sales distribution across dates
* Transaction frequency
* Data validation

---

# Business Analysis & SQL Queries

## Q1. Retrieve Sales for a Specific Date

### Business Significance

Businesses often analyze sales for a particular day to evaluate promotional campaigns, public holidays, festivals, or operational events that may have impacted sales performance.

```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```

---

## Q2. Clothing Transactions with Quantity Greater Than or Equal to 4 During November 2022

### Business Significance

Bulk purchases indicate strong customer demand and help businesses understand seasonal buying behavior. These insights support inventory planning and promotional strategies during peak shopping periods.

```sql
SELECT COUNT(*)
FROM retail_sales
WHERE category = 'Clothing'
  AND quantiy >= 4
  AND sale_date BETWEEN '2022-11-01' AND '2022-11-30';
```

---

## Q3. Calculate Total Sales and Customer Count for Each Product Category

### Business Significance

Category-wise revenue analysis helps businesses identify their most profitable product segments, enabling better inventory management, pricing strategies, and resource allocation.

```sql
SELECT
    category,
    SUM(total_sale) AS Net_Sales,
    COUNT(customer_id) AS Customers
FROM retail_sales
GROUP BY category;
```

---

## Q4. Analyze Category-wise Revenue Along with Average Customer Age

### Business Significance

Combining revenue with customer demographics helps businesses understand which age groups contribute most to different product categories. This information supports customer segmentation and targeted marketing.

```sql
SELECT
    category,
    SUM(total_sale) AS Net_Sales,
    COUNT(customer_id) AS Customers,
    AVG(age) AS Average_Age
FROM retail_sales
GROUP BY category;
```

---

## Q5. Identify High-Value Transactions

### Business Significance

High-value transactions contribute significantly to overall revenue. Identifying these purchases helps businesses recognize premium customers and design effective loyalty programs.

```sql
SELECT *
FROM retail_sales
WHERE total_sale > 1000;
```

---

## Q6. Analyze Sales Distribution by Gender and Product Category

### Business Significance

Understanding purchasing preferences across different customer demographics enables businesses to personalize marketing campaigns and improve product assortment.

```sql
SELECT
    category,
    gender,
    COUNT(transactions_id) AS Total_Transactions
FROM retail_sales
GROUP BY category, gender;
```

---

## Q7. Identify the Best Performing Month in Each Year

### Business Significance

Seasonal sales analysis helps businesses forecast demand, optimize inventory, allocate budgets efficiently, and schedule marketing campaigns during high-performing periods.

```sql
SELECT *
FROM
(
    SELECT
        YEAR(sale_date) AS Year,
        MONTH(sale_date) AS Month,
        AVG(total_sale) AS Average_Sales,
        RANK() OVER(
            PARTITION BY YEAR(sale_date)
            ORDER BY AVG(total_sale) DESC
        ) AS Rank_in_Year
    FROM retail_sales
    GROUP BY YEAR(sale_date), MONTH(sale_date)
) ranked_sales
WHERE Rank_in_Year = 1;
```

---

## Q8. Identify the Top 5 Customers Based on Total Revenue

### Business Significance

A small percentage of customers often contributes a significant portion of total revenue. Identifying these customers helps businesses improve customer retention and loyalty initiatives.

```sql
SELECT
    customer_id,
    SUM(total_sale) AS Sales
FROM retail_sales
GROUP BY customer_id
ORDER BY Sales DESC
LIMIT 5;
```

---

## Q9. Count Unique Customers Across Product Categories

### Business Significance

Measuring customer reach across different product categories helps businesses understand category popularity and identify opportunities for cross-selling.

```sql
SELECT
    category,
    COUNT(customer_id)
FROM
(
    SELECT DISTINCT customer_id, category
    FROM retail_sales
) AS dis
GROUP BY category;
```

---

## Q10. Analyze Sales by Time of Day

### Business Significance

Understanding customer shopping behavior throughout the day helps businesses optimize staffing, promotional timing, store operations, and inventory availability.

```sql
SELECT
    CASE
        WHEN HOUR(sale_time) < 12 THEN 'Morning'
        WHEN HOUR(sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
        ELSE 'Evening'
    END AS Shift,
    COUNT(*) AS Number_of_Orders
FROM retail_sales
GROUP BY Shift;
```

---

# Key Insights

The analysis provides insights into:

* Product categories contributing the highest revenue.
* Customer purchasing patterns across different demographics.
* High-value customers generating significant business revenue.
* Seasonal sales trends useful for forecasting demand.
* Customer shopping behavior during different times of the day.
* Category-wise customer engagement and purchasing preferences.

---

# SQL Concepts Demonstrated

* Database Creation (DDL)
* Data Cleaning
* Data Validation
* Filtering using `WHERE`
* Aggregate Functions (`SUM`, `COUNT`, `AVG`)
* `GROUP BY`
* `ORDER BY`
* Date and Time Functions
* Conditional Logic using `CASE`
* Window Functions (`RANK`)
* Subqueries
* Business-Oriented Data Analysis

---

# Tools & Technologies

* MySQL
* SQL
* Database Management System (DBMS)

---

# Project Highlights

* Designed and implemented a relational database for retail sales analysis.
* Performed data cleaning to ensure data quality and analytical accuracy.
* Solved 10 business-oriented analytical problems using SQL.
* Applied advanced SQL concepts including Window Functions and Subqueries.
* Converted raw transactional data into actionable business insights.
* Demonstrated SQL skills commonly required for Data Analyst, Business Analyst, and Data Science roles.

---

# Future Improvements

Potential enhancements for this project include:

* Creating SQL Views for reporting.
* Implementing Stored Procedures for reusable analysis.
* Optimizing queries using Indexes.
* Developing an interactive Power BI or Tableau dashboard.
* Expanding the dataset to include multiple stores and regions for comparative analysis.

---

# Author

**Akash Badgoti**

Artificial Intelligence & Data Science Undergraduate

Aspiring Data Analyst | Data Scientist | Machine Learning Enthusiast
