# 🛒 Amazon Sales Performance Analysis (April - May 2022)

**Identifying key sales drivers, optimizing logistics, and improving inventory strategy using SQL.**

[![SQL](https://img.shields.io/badge/Language-SQL-red)](https://en.wikipedia.org/wiki/SQL)
[![PostgreSQL](https://img.shields.io/badge/Database-PostgreSQL%2FSQLite-blue)](https://www.postgresql.org/)
[![Data Visualization](https://img.shields.io/badge/Visualization-Tableau%2FPowerBI-darkgreen)](https://www.tableau.com/en-gb)

---

## 📝 Project Goal

This project analyzes Amazon sales data using **advanced SQL techniques** to answer real-world business questions related to revenue performance, customer behavior, logistics efficiency, and operational risk.  
The focus is on translating business questions into performant, explainable SQL logic rather than simple aggregation.

---

## 🧠 Key Skills Demonstrated

- Window functions (`RANK`, `LAG`, `AVG() OVER`)
- Time-series analysis
- Binning & distributions
- Pivot-style conditional aggregation
- String analytics (`STRING_AGG`, `TRIM`, `NULLIF`)
- Business-driven SQL design

---

## 📊 Dataset

- **Source:** Kaggle – Amazon Sales Report (public dataset)
- **Time Period:** March–June 2022
- **Rows:** ~X
- **Key Columns:**  
  `order_id`, `order_date`, `amount`, `qty`, `status`, `category`, `style`, `fulfilment`, `ship_state`

---

## 🗂 Repository Structure

```
amazon-sales-analysis/
├── README.md # Project overview & business context
├── sql/
│ ├── 01_data_cleaning.sql # Staging, data cleansing, type conversion
│ └── 02_analysis_queries.sql # Business-driven analytical SQL
├── docs/
│ └── data_preparation.md # Detailed ETL design & rationale

```
