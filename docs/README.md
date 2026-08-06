# Retail Sales Analysis using SQL

## Project Overview

Retail businesses generate thousands of transactions every day, making it essential to transform raw sales data into actionable business insights. This project demonstrates how SQL can be used to clean, explore, and analyze retail transaction data to support data-driven decision-making.

Using MySQL, the project covers the complete analytics workflow—from database creation and data cleaning to exploratory data analysis (EDA) and solving real-world business problems using SQL. The analysis provides valuable insights into customer purchasing behavior, product performance, sales trends, and revenue generation.

---

## Business Problem

Retail organizations need to answer critical business questions such as:

- Which product categories generate the highest revenue?
- Who are the most valuable customers?
- How does customer purchasing behavior vary across demographics?
- Which months generate the highest sales?
- What time of day records the highest customer activity?
- How can these insights improve inventory planning and marketing strategies?

This project addresses these business questions through structured SQL analysis.

---

## Business Objectives

The primary objectives of this project are to:

- Analyze sales performance across different product categories.
- Identify top-performing customers based on total spending.
- Study customer purchasing patterns using demographic information.
- Discover seasonal sales trends for demand forecasting.
- Analyze shopping behavior across different times of the day.
- Generate business insights that support strategic decision-making.

---

# Tech Stack

| Technology | Purpose |
|------------|---------|
| MySQL | Database Management System |
| SQL | Data Cleaning & Business Analysis |
| Git | Version Control |
| GitHub | Project Hosting |

---

# Repository Structure

```
Retail-Sales-Analysis
│
├── data/
│   └── retail_sales.csv
│
├── sql/
│   ├── database_setup.sql
│   ├── data_cleaning.sql
│   ├── exploratory_analysis.sql
│   └── business_queries.sql
│
├── docs/
│   └── screenshots/
│
└── README.md
```

---

# Project Workflow

```
Retail Sales Dataset
        │
        ▼
Database Creation
        │
        ▼
Data Import
        │
        ▼
Data Cleaning
        │
        ▼
Exploratory Data Analysis
        │
        ▼
Business Analysis using SQL
        │
        ▼
Business Insights
```

---

# Dataset Information

The dataset consists of retail transaction records where each row represents a customer purchase.

| Attribute | Description |
|------------|-------------|
| Transaction ID | Unique transaction identifier |
| Sale Date | Date of purchase |
| Sale Time | Time of purchase |
| Customer ID | Customer identifier |
| Gender | Customer gender |
| Age | Customer age |
| Category | Product category |
| Quantity | Number of items purchased |
| Price per Unit | Selling price per unit |
| COGS | Cost of Goods Sold |
| Total Sale | Total transaction amount |

---

# Database Schema

```sql
CREATE DATABASE RetailSalesAnalysis_utf;

USE RetailSalesAnalysis_utf;

CREATE TABLE retail_sales(
    transactions_id INT PRIMARY KEY,
    sale_date DATE,
    sale_time TIME,
    customer_id INT,
    gender VARCHAR(15),
    age INT,
    category VARCHAR(20),
    quantiy INT,
    price_per_unit FLOAT,
    cogs FLOAT,
    total_sale FLOAT
);
```

---

# Data Cleaning

## Business Significance

Data quality plays a crucial role in generating reliable business insights. Missing or incomplete records can produce inaccurate reports, incorrect revenue calculations, and misleading customer analytics.

Therefore, the dataset was validated before performing any business analysis.

### Check Missing Values

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

# Exploratory Data Analysis

## Business Significance

Before answering business questions, it is important to understand the overall structure and quality of the dataset. Exploratory Data Analysis provides a high-level overview of the data and validates its readiness for analysis.

Example:

```sql
SELECT COUNT(*)
FROM retail_sales;
```

EDA includes:

- Total Transactions
- Product Category Distribution
- Customer Demographics
- Sales Distribution
- Data Validation

---

# Business Questions & Analysis

---

## Q1. Retrieve Sales for a Specific Date

### Business Significance

Businesses often analyze sales for a particular day to evaluate the impact of promotional campaigns, public holidays, festivals, or operational events.

```sql
SELECT *
FROM retail_sales
WHERE sale_date='2022-11-05';
```

---

## Q2. Count Clothing Transactions with Quantity Greater than or Equal to 4 During November 2022

### Business Significance

Bulk purchases indicate seasonal demand and customer buying behavior. These insights help businesses optimize inventory and promotional strategies.

```sql
SELECT COUNT(*)
FROM retail_sales
WHERE category='Clothing'
AND quantiy>=4
AND sale_date BETWEEN '2022-11-01' AND '2022-11-30';
```

---

## Q3. Calculate Total Sales by Product Category

### Business Significance

Understanding category-wise revenue helps businesses identify their highest-performing product segments and allocate resources effectively.

```sql
SELECT
category,
SUM(total_sale) AS Net_Sales,
COUNT(customer_id) AS Customers
FROM retail_sales
GROUP BY category;
```

---

## Q4. Calculate Category-wise Revenue Along with Average Customer Age

### Business Significance

Combining customer demographics with revenue enables businesses to identify the target audience for each product category and improve marketing strategies.

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

High-value purchases contribute significantly to overall revenue and help businesses identify premium customer segments.

```sql
SELECT *
FROM retail_sales
WHERE total_sale>1000;
```

---

## Q6. Analyze Purchases by Gender and Product Category

### Business Significance

Understanding purchasing preferences across customer demographics supports personalized marketing campaigns and inventory planning.

```sql
SELECT
category,
gender,
COUNT(transactions_id) AS Total_Transactions
FROM retail_sales
GROUP BY category,gender;
```

---

## Q7. Identify the Best Performing Month in Each Year

### Business Significance

Seasonal sales analysis helps businesses forecast demand, optimize inventory, and schedule marketing campaigns effectively.

```sql
SELECT *
FROM(
SELECT
YEAR(sale_date) AS Year,
MONTH(sale_date) AS Month,
AVG(total_sale) AS Average_Sales,
RANK() OVER(
PARTITION BY YEAR(sale_date)
ORDER BY AVG(total_sale) DESC
) AS Rank_in_Year
FROM retail_sales
GROUP BY YEAR(sale_date),MONTH(sale_date)
) ranked_sales
WHERE Rank_in_Year=1;
```

---

## Q8. Identify the Top 5 Customers by Revenue

### Business Significance

A small percentage of customers usually contributes a significant share of business revenue. Identifying these customers supports loyalty programs and customer retention.

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

This analysis measures customer reach across different product categories and identifies opportunities for cross-selling.

```sql
SELECT
category,
COUNT(customer_id)
FROM(
SELECT DISTINCT customer_id,category
FROM retail_sales
) AS dis
GROUP BY category;
```

---

## Q10. Analyze Customer Purchases by Time of Day

### Business Significance

Understanding customer activity during different times of the day helps businesses optimize staffing, promotional timing, and operational planning.

```sql
SELECT
CASE
WHEN HOUR(sale_time)<12 THEN 'Morning'
WHEN HOUR(sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
ELSE 'Evening'
END AS Shift,
COUNT(*) AS Number_of_Orders
FROM retail_sales
GROUP BY Shift;
```

---

# Key Business Insights

The SQL analysis provides insights into:

- Revenue contribution by different product categories.
- Customer purchasing behavior across age groups and gender.
- High-value customers driving significant business revenue.
- Seasonal sales trends that support demand forecasting.
- Peak shopping hours useful for operational planning.
- Category-wise customer engagement and popularity.

---

# Business KPIs Covered

- Total Revenue
- Customer Count
- Sales by Product Category
- Average Customer Age
- High-Value Transactions
- Top Customers
- Monthly Sales Performance
- Time-wise Order Distribution
- Category-wise Customer Reach

---

# SQL Concepts Demonstrated

- Database Creation (DDL)
- Data Manipulation (DML)
- Data Cleaning
- Aggregate Functions
- GROUP BY
- ORDER BY
- WHERE Clause
- CASE Statements
- Date Functions
- Window Functions (RANK)
- Subqueries
- Business-Oriented SQL Analysis

---

# Project Outcomes

- Designed and implemented a relational retail sales database.
- Cleaned and validated transactional data.
- Solved 10 business-oriented analytical problems using SQL.
- Applied advanced SQL concepts including Window Functions and Subqueries.
- Generated actionable business insights from retail sales data.
- Demonstrated SQL skills aligned with Data Analyst and Business Analyst roles.

---

# Future Scope

Possible enhancements include:

- Build an interactive Power BI dashboard.
- Develop reusable SQL Views.
- Implement Stored Procedures.
- Optimize query performance using Indexes.
- Automate the ETL pipeline using Python.
- Expand analysis to multiple stores and geographical regions.

---

# Author

**Akash Badgoti**

Artificial Intelligence & Data Science Undergraduate

Aspiring Data Analyst | Data Scientist | Machine Learning Enthusiast
