# Databricks 14 Days AI Challenge

A hands-on learning journey through **Apache Spark**, **PySpark**, and **Delta Lake** on Databricks, using an e-commerce transactions dataset (50,000 records).

## Dataset

| Column | Type |
|---|---|
| Transaction_ID | Long |
| User_Name | String |
| Age | Long |
| Country | String |
| Product_Category | String |
| Purchase_Amount | Double |
| Payment_Method | String |
| Transaction_Date | Date |

## Day-wise Progress

| Day | Topic | Key Concepts |
|---|---|---|
| **1** | Spark & PySpark Basics | DataFrames, `filter`, `orderBy`, `groupBy`, `withColumn`, Spark SQL, `saveAsTable` |
| **2** | Spark Fundamentals (contd.) | Core Spark concepts (reference PDF) |
| **3** | Advanced PySpark | UDFs, Window Functions (running totals), Aggregations (top categories by revenue) |
| **4** | Delta Lake Basics | Delta table creation, schema enforcement, deduplication, `DESCRIBE DETAIL` |
| **5** | Delta Lake Operations | Managed Delta tables, incremental merge (upsert), `MERGE INTO`, time travel |
| **6** | Medallion Architecture | Bronze / Silver / Gold layers, data quality checks, schema separation |
| **7–14** | *In Progress* | — |

## Tech Stack

- **Platform**: Databricks Community Edition
- **Language**: Python (PySpark)
- **Storage Format**: Delta Lake
- **Data**: `ecommerce_transactions.csv`

## How to Run

1. Upload `ecommerce_transactions.csv` to Databricks.
2. Open the Day-wise notebooks (`.ipynb`) in Databricks.
3. Run cells sequentially — each notebook is self-contained.
