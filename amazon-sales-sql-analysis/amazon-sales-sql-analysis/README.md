# 🛒 Amazon Sales Performance Analysis (April - May 2022)

**Identifying key sales drivers, optimizing logistics, and improving inventory strategy using SQL.**

[![SQL](https://img.shields.io/badge/Language-SQL-red)](https://en.wikipedia.org/wiki/SQL)
[![PostgreSQL](https://img.shields.io/badge/Database-PostgreSQL%2FSQLite-blue)](https://www.postgresql.org/)
[![Data Visualization](https://img.shields.io/badge/Visualization-Tableau%2FPowerBI-darkgreen)](https://www.tableau.com/en-gb)

---

## 📝 Project Goal

This project executes an end-to-end data analysis using **SQL** on the Amazon Sale Report (129K transactions). The primary focus is to answer critical business questions to improve profitability and operational efficiency within the e-commerce framework.

The analysis specifically showcases:
* **Advanced SQL Techniques:** Utilizing Common Table Expressions (CTEs), Conditional Aggregation, and Window Functions.
* **Business Intelligence:** Translating quantitative findings into direct, actionable business strategies.

---

## 🛠️ Data Preparation and Tools

The raw data was loaded into a database environment (e.g., PgAdmin connected to a PostgreSQL instance).

### Key Data Cleaning Steps (SQL Focus)

| Cleaning Task | SQL Rationale |
| :--- | :--- |
| **Data Type Conversion** | Used `CAST(Date AS DATE)` to convert the date column from text to a proper `DATE` type for time-series analysis. |
| **Missing Value Imputation** | Employed `COALESCE(Amount, 0)` in all revenue calculations to safely treat null `Amount` values (which typically correlate with cancelled orders) as zero revenue. |
| **Data Filtering** | Filtered out rows where `ship-state` was null for geographic analysis. |

---

## 🎯 Key Business Questions & Analysis

This section features **seven highly relevant questions** for any e-commerce or sales operation.

### Q1. Fulfillment Channel Contribution (Logistics Efficiency)

**Goal:** Determine the revenue and quantity contribution of Amazon Fulfilled vs. Merchant Fulfilled orders to guide logistics strategy.

```sql
-- Query 1: Fulfillment Channel Contribution
WITH GrandTotals AS (
    SELECT
        SUM(Qty) AS TotalQty,
        SUM(COALESCE(Amount, 0)) AS TotalRevenue
    FROM "Amazon Sale Report"
)
SELECT
    t1.Fulfilment,
    SUM(t1.Qty) AS TotalUnitsSold,
    SUM(COALESCE(Amount, 0)) AS TotalRevenue,
    ROUND((SUM(t1.Qty) * 100.0) / gt.TotalQty, 2) AS QtyPercentage,
    ROUND((SUM(COALESCE(Amount, 0)) * 100.0) / gt.TotalRevenue, 2) AS RevenuePercentage
FROM
    "Amazon Sale Report" t1
CROSS JOIN GrandTotals gt
GROUP BY
    t1.Fulfilment, gt.TotalQty, gt.TotalRevenue
ORDER BY
    TotalRevenue DESC;
```

### Q2. Top 5 Selling Styles (Inventory Optimization)
**Goal:** Identify the most crucial product styles for inventory planning and merchandising campaigns.

```sql
-- Query 2: Top 5 Selling Styles
SELECT
    Style,
    Category,
    SUM(Qty) AS TotalUnitsSold,
    SUM(COALESCE(Amount, 0)) AS TotalRevenue
FROM
    "Amazon Sale Report"
WHERE
    Status NOT IN ('Cancelled', 'Unshipped') -- Focus on delivered/shipped sales
GROUP BY
    Style, Category
ORDER BY
    TotalUnitsSold DESC
LIMIT 5;
```
### Q3. Daily Sales Performance (Time-Series Trend)
**Goal:** Analyze sales volatility and identify peak/trough trading days for potential resource or marketing adjustments.

```SQL
-- Query 3: Daily Sales Performance
SELECT
    CAST(Date AS DATE) AS SaleDate,
    COUNT("Order ID") AS TotalOrders,
    SUM(Qty) AS TotalUnitsSold,
    SUM(COALESCE(Amount, 0)) AS DailyRevenue
FROM
    "Amazon Sale Report"
GROUP BY
    SaleDate
ORDER BY
    SaleDate;
```

### Q4. Geographic Cancellation Rates (Logistics Bottlenecks)
**Goal:** Pinpoint specific shipping states with poor delivery service, leading to high cancelled orders and customer dissatisfaction.
```sql
-- Query 4: Geographic Cancellation Rates
WITH StatePerformance AS (
    SELECT
        "ship-state",
        COUNT("Order ID") AS TotalOrders,
        -- Conditional aggregation to count cancelled orders
        SUM(CASE WHEN Status = 'Cancelled' THEN 1 ELSE 0 END) AS CancelledOrders
    FROM 
        "Amazon Sale Report"
    WHERE 
        "ship-state" IS NOT NULL
    GROUP BY 
        1
    HAVING 
        COUNT("Order ID") > 500 -- Analyze only high-volume states
)
SELECT
    "ship-state",
    CancelledOrders,
    ROUND((CancelledOrders * 100.0) / TotalOrders, 2) AS CancellationRate
FROM
    StatePerformance
ORDER BY
    CancellationRate DESC
LIMIT 5;
```

### Q5. B2B vs. B2C Average Order Value (Segmentation & Pricing)
**Goal:** Compare average order value (AOV) between B2B (Business) and B2C (Consumer) customers to tailor pricing or bundling strategies.

```sql
-- Query 5: B2B vs B2C Average Order Value
SELECT
    CASE WHEN B2B = TRUE THEN 'B2B (Business)' ELSE 'B2C (Consumer)' END AS CustomerType,
    COUNT("Order ID") AS TotalOrders,
    AVG(COALESCE(Amount, 0)) AS AverageOrderValue,
    SUM(COALESCE(Amount, 0)) AS TotalRevenue
FROM
    "Amazon Sale Report"
GROUP BY
    CustomerType;
```

### Q6. Fulfillment Speed Analysis (Service Level Impact)
**Goal:** Analyze sales volume and average revenue split by ship-service-level to justify premium shipping costs.

```sql
-- Query 6: Fulfillment Speed Analysis
SELECT
    "ship-service-level",
    COUNT("Order ID") AS TotalOrders,
    SUM(COALESCE(Amount, 0)) AS TotalRevenue,
    AVG(COALESCE(Amount, 0)) AS AOV
FROM
    "Amazon Sale Report"
GROUP BY
    "ship-service-level"
ORDER BY
    TotalRevenue DESC;
```
### Q7. High-Value Product Identification (ASIN Performance)
**Goal:** Identify the most valuable single products (ASINs) by total revenue generated, regardless of volume.

```sql
-- Query 7: High-Value Product ASINs
SELECT
    ASIN,
    Style,
    Category,
    SUM(COALESCE(Amount, 0)) AS TotalRevenueGenerated
FROM
    "Amazon Sale Report"
GROUP BY
    ASIN, Style, Category
ORDER BY
    TotalRevenueGenerated DESC
LIMIT 10;
```

## 📂 Repository Structure

amazon-sales-sql-analysis/ ├── README.md <-- (This detailed report) ├── SQL_Queries.sql <-- A single file containing all query code for PgAdmin ├── data/ │ └── Amazon Sale Report.csv <-- Original data ├── results/ <-- CSV outputs of the queries └── visualizations/ <-- PNG/JPEG images of supporting charts

