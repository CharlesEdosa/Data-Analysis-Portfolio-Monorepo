# 🛒 # Amazon Sales Analysis — SQL Analytics Project

**Identifying key sales drivers, optimizing logistics, and improving inventory strategy using SQL.**

[![SQL](https://img.shields.io/badge/Language-SQL-red)](https://en.wikipedia.org/wiki/SQL)
[![PostgreSQL](https://img.shields.io/badge/Database-PostgreSQL%2FSQLite-blue)](https://www.postgresql.org/)
[![Data Visualization](https://img.shields.io/badge/Visualization-Tableau%2FPowerBI-darkgreen)](https://www.tableau.com/en-gb)

---

# Amazon Sales Analysis — SQL Analytics Project

## 🎯 Project Goal
This project analyzes Amazon sales data using advanced SQL techniques to answer real-world business questions related to **revenue performance, customer behavior, logistics efficiency, and operational risk**.

The emphasis is on translating business problems into **performant, explainable SQL logic**, rather than relying on simple aggregations or dashboard-only insights.

---

## 🧠 Key Skills Demonstrated
- Window functions (`RANK`, `LAG`, `AVG() OVER`)
- Time-series analysis
- Binning & distribution analysis
- Pivot-style conditional aggregation
- String analytics (`STRING_AGG`, `TRIM`, `NULLIF`)
- Business-driven SQL query design
- Data quality handling in strongly typed databases (PostgreSQL)

---

## 📊 Dataset
- **Source:** Kaggle — *Amazon Sales Report* (public dataset)
- **Time Period:** March–June 2022
- **Rows:** ~120k+ (after cleaning and validation)
- **Key Columns:**  
  `order_id`, `order_date`, `amount`, `qty`, `status`,  
  `category`, `style`, `fulfilment`, `ship_state`

---

## 🗂 Repository Structure

```
amazon-sales-sql-analysis/
├── README.md # Project overview & business context
├── amazon_sales_queries/
│ ├── 01_data_cleaning.sql # Staging, data cleansing, type conversion
│ └── 02_analysis_queries.sql # Business-driven analytical SQL
├── data_preparation.md # Detailed ETL design & rationale
```

---

## 🛠️ Data Preparation and Tools
The dataset required a **staged ETL process** due to multiple structural and data-quality issues that prevented direct ingestion into PostgreSQL, including:

- Non-standard CSV structure (decorative title rows before headers)
- Implicit NULL values represented as empty strings or whitespace
- Type incompatibilities when casting to `NUMERIC` and `BOOLEAN`

To ensure data integrity, fault tolerance, and controlled type conversion, the data was first loaded into a staging table and progressively normalized.

📄 **Detailed ETL design and cleaning logic:**  
[`data_preparation.md`](data_preparation.md)

---

## 🔍 Business Questions

This analysis was designed to support commercial and operational decision-making.  
Specifically, it answers the following business questions:

1. **Revenue Concentration**
   - Which product styles contribute the most revenue within each category, and how do they rank against competitors in the same category?
   - How are customer orders distributed across revenue bands (e.g., ₹0–₹500, ₹500–₹1000, etc.)?
   - Which fulfillment methods outperform or underperform their category’s average revenue contribution?
   - What percentage of total revenue is driven by the top 20% of products?

2. **Operational Risk & Revenue Leakage**
   - Which shipping service levels generate high volume but low average order value?
   - Which products have the widest price variability between their minimum and maximum order values?
     

3. **Sales Performance Over Time**
   - How does daily revenue trend over time, and what is the rolling 7-day average to smooth volatility?
   - Which months generate the highest revenue, how does revenue fluctuate month-over-month, and what intra-year seasonal patterns can be         observed?
   - Which products show declining performance compared to their previous sales period?

4. **Geographic & Logistics Performance**
   - What product combinations are most frequently purchased together in the same order?
   - Which states show unusually high cancellation rates compared to the national average?
---

## 🧩 Tools & Environment
- PostgreSQL
- SQL (ANSI-compliant, PostgreSQL extensions where appropriate)
- Git & GitHub for version control and documentation

---

## 📌 Notes
This repository prioritizes **clarity, correctness, and business relevance** over excessive abstraction.  
Where appropriate, intermediate steps are preserved to make analytical decisions auditable and easy to follow.


