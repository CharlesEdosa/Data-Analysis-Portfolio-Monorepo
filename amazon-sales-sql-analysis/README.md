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
| **Renamed column** | Used `ALTER TABLE amazon_sales_report... RENAME "Order ID" TO order_id` renamed some columns to make querying easier |
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
    FROM amazon_sales_report
)
SELECT
    t1.Fulfilment,
    SUM(t1.Qty) AS TotalUnitsSold,
    SUM(COALESCE(Amount, 0)) AS TotalRevenue,
    ROUND((SUM(t1.Qty) * 100.0) / gt.TotalQty, 2) AS QtyPercentage,
    ROUND((SUM(COALESCE(Amount, 0)) * 100.0) / gt.TotalRevenue, 2) AS RevenuePercentage
FROM
    amazon_sales_report t1
CROSS JOIN GrandTotals gt -- I used Cross Join because the CTE returns 1 row
GROUP BY t1.Fulfilment, gt.TotalQty, gt.TotalRevenue
ORDER BY
    TotalRevenue DESC;
```

### Q2. Top 5 Selling Styles (Inventory Optimization)
**Goal:** Identify the most crucial product styles for inventory planning and merchandising campaigns.

```sql
-- Query 2: Top 5 Selling Styles
SELECT style, category, SUM(Qty) AS total_units, SUM(COALESCE(AMOUNT,0)) AS total_revenue
FROM amazon_sales_report
WHERE status ILIKE 'shipped%'              
  AND status NOT ILIKE '%returned%'        
  AND status NOT ILIKE '%rejected%'        
  AND status NOT ILIKE '%canceled%'        
  AND status NOT ILIKE '%pending%'
GROUP BY 1,2
ORDER BY total_units DESC
LIMIT 5;
-- I identified the top 5 selling styles by total units sold, considering only completed sales, to guide inventory and merchandising decisions.
```

### Q3. Daily Sales Performance (Time-Series Trend)
**Goal:** Analyze sales volatility and identify peak/trough trading days for potential resource or marketing adjustments.

```SQL
-- Query 3: Daily Sales Performance
WITH daily AS (
    SELECT 
        CAST(date AS DATE) AS actual_date, 
        TRIM(TO_CHAR(CAST(date AS DATE), 'Day')) AS day_name,
        EXTRACT(ISODOW FROM CAST(date AS DATE)) AS day_number,
        SUM(COALESCE(amount,0)) AS total_amount,
        COUNT(order_id) AS number_of_transactions
    FROM amazon_sales_report
    GROUP BY 1,2,3
)

(
    -- Daily time-series trend (for volatility analysis)
    SELECT
        'Daily Trend' AS report_type,
        actual_date,
        day_name,
        day_number,
        total_amount,
        number_of_transactions
    FROM daily
    ORDER BY actual_date
)
UNION ALL

(
    -- Peak & trough trading days (highest → lowest performance)
    SELECT
        'Peak/Trough' AS report_type,
        actual_date,
        day_name,
        day_number,
        total_amount,
        number_of_transactions
    FROM daily
    ORDER BY total_amount DESC
);
```

### Q4. Geographic Cancellation Rates (Logistics Bottlenecks)
**Goal:** Pinpoint specific shipping states with poor delivery service, leading to high cancelled orders and customer dissatisfaction.
```sql
-- Query 4: Geographic Cancellation Rates
WITH StatePerformance AS (
    SELECT
        ship_state,
        COUNT(order_id) AS TotalOrders,
        -- Conditional aggregation to count cancelled orders
        SUM(CASE WHEN Status = 'Cancelled' THEN 1 ELSE 0 END) AS CancelledOrders
    FROM 
        amazon_sales_report
    WHERE 
        ship_state IS NOT NULL
    GROUP BY 
        1
    HAVING 
        COUNT(order_id) > 500 -- Analyze only high-volume states
)

SELECT
    ship_state,
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
	AVG(COALESCE(amount,0)) avg_order_value, SUM(COALESCE(amount,0)) total_amount, COUNT(order_id) total_orders,
	CASE 
		WHEN B2B=TRUE THEN 'business'
		ELSE 'individual'
		END AS customertype
FROM amazon_sales_report
GROUP BY 4
```

### Q6. Fulfillment Speed Analysis (Service Level Impact)
**Goal:** Analyze sales volume and average revenue split by ship-service-level to justify premium shipping costs.

```sql
-- Query 6: Fulfillment Speed Analysis
SELECT 
    ship_service_level, 
    COUNT(order_id) total_orders, 
    ROUND(SUM(COALESCE(amount, 0)):: NUMERIC,2) total_amount, -- CAST to numeric to enable me use the ROUND function
    ROUND(AVG(COALESCE(amount, 0)):: NUMERIC,2) avg_amount
FROM amazon_sales_report
GROUP BY 1

```

### Q7. High-Value Product Identification (ASIN Performance)
**Goal:** Identify the most valuable single products (ASINs) by total revenue generated, regardless of volume.

```sql
-- Query 7: Top 10 High-Value Product ASINs
SELECT  
    ASIN,
    COUNT(order_id) total_orders, 
    SUM(COALESCE(amount, 0)) total_amount
FROM amazon_sales_report
GROUP BY 1
ORDER BY total_amount DESC
LIMIT 10

```























































