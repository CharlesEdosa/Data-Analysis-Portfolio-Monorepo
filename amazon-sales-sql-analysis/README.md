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

## 📈 Analytical Focus
The SQL analyses in this project are designed to answer questions such as:

- How is revenue distributed across product categories and fulfillment types?
- Which order statuses contribute most to revenue leakage or operational risk?
- How does sales concentration change over time?
- Are there regional or logistical patterns affecting delivery outcomes?

Each query is written with **clear business intent**, optimized joins, and transparent logic suitable for review and extension.

---

## 🧩 Tools & Environment
- PostgreSQL
- SQL (ANSI-compliant, PostgreSQL extensions where appropriate)
- Git & GitHub for version control and documentation

---

## 📌 Notes
This repository prioritizes **clarity, correctness, and business relevance** over excessive abstraction.  
Where appropriate, intermediate steps are preserved to make analytical decisions auditable and easy to follow.


