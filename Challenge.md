# Databricks 14 Days AI Challenge

At [**Indian Data Club**](http://indiandataclub.com/), we’re on a mission to empower the next generation with practical AI and data skills. Our **Databricks 14-Days AI Challenge** is designed to help beginners build a strong foundation Databricks through daily learning, hands-on practice, and real-world problem solving.

This Challenge is Sponsored by [Databricks](https://www.databricks.com/) and organised by [**Codebasics**](https://codebasics.io/) and [Indian Data Club](https://www.indiandataclub.com/).

---

**Before starting the challenge, watch this recording for the Notion overview.**

[IDC Databricks Induction Session: Complete Guide to The Challenge](https://youtu.be/yWTGo0c_2WQ?si=tyG9ndJt3-kYJaHx)

---

[𝗖𝗵𝗮𝗹𝗹𝗲𝗻𝗴𝗲 𝗦𝘁𝗿𝘂𝗰𝘁𝘂𝗿𝗲 (𝗧𝘄𝗼 𝗣𝗵𝗮𝘀𝗲𝘀)](https://www.notion.so/2e39b695c41180a6aad3d5527b77b145?pvs=21)

---

[Essentials](https://www.notion.so/2e39b695c41180fd88d3cfda8304fd3e?pvs=21)

---

**Here is the link to sign up for Databricks: [Click Here](https://bit.ly/4nK0NTN)**

---

## DAY 0 – Setup & Data Loading (Prerequisites)

### Overview

Before starting Day 1, complete this setup to load the e-commerce dataset directly from Kaggle into your Databricks workspace.

---

### **Step 1: Create Databricks Account**

1. Go to [Databricks Community Edition](https://bit.ly/4nK0NTN)
2. Sign up for free account
3. Verify email and log in
4. Create a cluster (use default settings)

---

### **Step 2: Get Kaggle API Credentials**

1. Go to [Kaggle.com](https://www.kaggle.com/) and log in
2. Click on your profile picture → **Account**
3. Scroll to **API** section → Click **Create New API Token**
4. Download `kaggle.json` (contains your credentials)
5. Open the file and note your `username` and `key`

---

### **Step 3: Load Data in Databricks**

Create a new notebook in Databricks and run these cells:

### **Notebook:**

### 1. Install Dependencies

```markdown
!pip install kaggle
```

### 2. Configure Kaggle Credentials

```python
import os

os.environ["KAGGLE_USERNAME"] = "your_username"
os.environ["KAGGLE_KEY"] = "your_key"

print("Kaggle credentials configured!")
```

### 3. Create Database Schema

```python
spark.sql("""
CREATE SCHEMA IF NOT EXISTS workspace.ecommerce
""")
```

### 4. Create Volume for Data Storage

```python
spark.sql("""
CREATE VOLUME IF NOT EXISTS workspace.ecommerce.ecommerce_data
""")
```

### 5. Download Dataset from Kaggle

```bash
cd /Volumes/workspace/ecommerce/ecommerce_data
kaggle datasets download -d mkechinov/ecommerce-behavior-data-from-multi-category-store
```

### 6. Extract Downloaded Dataset

```bash
cd /Volumes/workspace/ecommerce/ecommerce_data
unzip -o ecommerce-behavior-data-from-multi-category-store.zip
ls -lh
```

### 7. Clean Up Zip File

```bash
cd /Volumes/workspace/ecommerce/ecommerce_data
rm -f ecommerce-behavior-data-from-multi-category-store.zip
ls -lh
```

### 8. Restart Python Environment

```python
%restart_python
```

### 9. Load November 2019 Data

```python
df_n = spark.read.csv("/Volumes/workspace/ecommerce/ecommerce_data/2019-Nov.csv")
```

### 10. Load October 2019 Data

```python
df = spark.read.csv("/Volumes/workspace/ecommerce/ecommerce_data/2019-Oct.csv")
```

### 11. Display Dataset Statistics and Schema

```python
print(f"October 2019 - Total Events: {df.count():,}")
print("\n" + "="*60)
print("SCHEMA:")
print("="*60)
df.printSchema()
```

### 12. Display Sample Data

```python
print("\n" + "="*60)
print("SAMPLE DATA (First 5 rows):")
print("="*60)
df.show(5, truncate=False)
```

### For better understanding, check the setup guide video: https://youtu.be/nHGMcrxHqrA

---

### **Expected Schema**

After loading, your dataset will have **9 columns**:

| Column | Type | Description | Notes |
| --- | --- | --- | --- |
| event_time | timestamp | When event happened (UTC) | Format: YYYY-MM-DD HH:MM:SS UTC |
| event_type | string | Type of event | Values: view, cart, purchase, remove_from_cart |
| product_id | long | Unique product identifier | Numeric ID |
| category_id | long | Category identifier | Numeric ID |
| category_code | string | Category hierarchy | e.g., "electronics.smartphone" (can be null) |
| brand | string | Product brand | Lowercase (can be null) |
| price | double | Product price in USD | Positive values |
| user_id | long | Permanent user identifier | Numeric ID |
| user_session | string | Session identifier | UUID format, changes per session |

---

### **Dataset Size Reference**

| File | Events | Size | Recommended Use |
| --- | --- | --- | --- |
| **2019-Oct.csv** | ~4.2M | ~1.1 GB | Days 1-3 (learning basics) |
| **2019-Nov.csv** | ~9.3M | ~2.2 GB | Days 4+ (full analysis) |
| **Combined** | ~13.5M | ~3.3 GB | Days 8+ (comprehensive analysis) |

**💡 Pro Tip:** Start with October data for faster iterations, then scale to combined dataset

---

### **Troubleshooting**

- **Issue: Kaggle credentials not working**
    
    ```python
    # Method 1: Set credentials directly
    os.environ['KAGGLE_USERNAME'] = "your_username"
    os.environ['KAGGLE_KEY'] = "your_key"
    
    # Method 2: Verify kaggle config
    !cat ~/.kaggle/kaggle.json
    
    ```
    
- **Issue: File not found in DBFS**
    
    ```python
    # Check DBFS contents
    display(dbutils.fs.ls("/FileStore/ecommerce_data/"))
    
    # Alternative path check
    dbutils.fs.ls("dbfs:/FileStore/ecommerce_data/")
    
    ```
    
- **Issue: Memory errors with large dataset**
    
    ```python
    # Use sampling for testing
    sampled = load_ecommerce_data("Oct", sample_fraction=0.1)
    print(f"Sampled data: {sampled.count():,} rows")
    
    ```
    
- **Issue: Schema inference problems**
    
    ```python
    # Explicitly define schema
    from pyspark.sql.types import StructType, StructField, TimestampType, StringType, LongType, DoubleType
    
    schema = StructType([
        StructField("event_time", TimestampType(), True),
        StructField("event_type", StringType(), True),
        StructField("product_id", LongType(), True),
        StructField("category_id", LongType(), True),
        StructField("category_code", StringType(), True),
        StructField("brand", StringType(), True),
        StructField("price", DoubleType(), True),
        StructField("user_id", LongType(), True),
        StructField("user_session", StringType(), True)
    ])
    
    events = spark.read.csv("/FileStore/ecommerce_data/2019-Oct.csv",
                            header=True, schema=schema)
    
    ```

### **Alternative: Manual Upload (If Kaggle API Doesn't Work)**

1. **Download manually:**
    - Go to [Kaggle Dataset](https://www.kaggle.com/datasets/mkechinov/ecommerce-behavior-data-from-multi-category-store)
    - Click "Download" button
    - Extract `2019-Oct.csv` and `2019-Nov.csv`
2. **Upload to Databricks:**
    - Method A: Click **Data** → **Create Table** → Upload files
    - Method B: Drag and drop files into notebook cell
3. **Note the path:**

```python
   # Path will be shown after upload, typically:
   # /FileStore/tables/2019_Oct.csv

   events = spark.read.csv("/FileStore/tables/2019_Oct.csv",
                          header=True, inferSchema=True)

```

---

### ✅ **Setup Complete Checklist**

- [ ]  Databricks account created and cluster running
- [ ]  Kaggle credentials configured
- [ ]  Dataset downloaded (2019-Oct.csv, 2019-Nov.csv)
- [ ]  Files uploaded to DBFS
- [ ]  Data loaded successfully (verified with count)
- [ ]  Schema validated (9 columns confirmed)
- [ ]  Sample queries executed successfully

---

### 🚀 **Ready for Day 1!**

Once all checklist items are complete, you're ready to start **Day 1: Platform Setup & First Steps**

**Quick Start Code for Day 1:**

```python
# Load your data
events = load_ecommerce_data("Oct")

# Verify it's working
print(f"✅ Ready to go! Loaded {events.count():,} events")
events.show(5)

# Your Day 1 challenges start here...

```

---

### 📚 **Additional Resources**

- [Kaggle Dataset Page](https://www.kaggle.com/datasets/mkechinov/ecommerce-behavior-data-from-multi-category-store)
- [Databricks Documentation](https://docs.databricks.com/)
- [PySpark DataFrame Guide](https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql/dataframe.html)
- Dataset provided by [REES46 Open CDP](https://rees46.com/en/open-cdp)

---

## **Your daily challenge starts from here.👇**

## LinkedIn post structure:

![Screenshot 2026-01-09 at 8.18.51 AM.png](attachment:1261b76c-66e6-4b91-b9ec-04ef82971b56:Screenshot_2026-01-09_at_8.18.51_AM.png)

## PHASE 1: FOUNDATION (Days 1-4)

### **DAY 1 (09/01/26)– Platform Setup & First Steps**

### Learn:

- Why Databricks vs Pandas/Hadoop?
- Lakehouse architecture basics
- Databricks workspace structure
- Industry use cases (Netflix, Shell, Comcast)

### 🛠️ Tasks:

1. Create Databricks Community Edition account
2. Navigate Workspace, Compute, Data Explorer
3. Create first notebook
4. Run basic PySpark commands

![14_Days Databricks daily Challenge tasks.png](attachment:d6431225-f7fd-404b-808b-a0d222012070:14_Days_Databricks_daily_Challenge_tasks.png)

### 📝 Practice:

```python
# Create simple DataFrame
data = [("iPhone", 999), ("Samsung", 799), ("MacBook", 1299)]
df = spark.createDataFrame(data, ["product", "price"])
df.show()

# Filter expensive products
df.filter(df.price > 1000).show()

```

### 🔗 Resources:

- [Databricks Trial](https://www.databricks.com/try-databricks)
- [Quickstart Guide](https://docs.databricks.com/en/introduction/)
- ✅ **Submit your day 1 progress using this form:**
    
    **1. Share your learning and build online presence.**
    
    Post your daily learning on **LinkedIn, X (Twitter), Instagram (Reels), or GitHub tagging @Databricks, @Codebasics and @indiandataclub along with #DatabricksWithIDC.**
    
    **2. Fill the submission form:** https://forms.office.com/r/GT2BquTRnH ****
    

---

### **DAY 2 (10/01/26) – Apache Spark Fundamentals**

### Learn:

- Spark architecture (driver, executors, DAG)
- DataFrames vs RDDs
- Lazy evaluation
- Notebook magic commands (`%sql`, `%python`, `%fs`)

### 🛠️ Tasks:

1. Upload sample e-commerce CSV
2. Read data into DataFrame
3. Perform basic operations: select, filter, groupBy, orderBy
4. Export results

![2.png](attachment:b6281e90-e57d-4617-9d7b-4ed2187aab99:2.png)

### 📝 Practice:

```python
# Load data
events = spark.read.csv("/path/to/sample.csv", header=True, inferSchema=True)

# Basic operations
events.select("event_type", "product_name", "price").show(10)
events.filter("price > 100").count()
events.groupBy("event_type").count().show()
top_brands = events.groupBy("brand").count().orderBy("count", ascending=False).limit(5)

```

### 🔗 Resources:

- [PySpark Guide](https://docs.databricks.com/pyspark/)
- [Spark SQL Guide](https://spark.apache.org/docs/latest/sql-programming-guide.html)
- ✅ **Submit your day 2 progress using this form:**
    - **1. Share your learning and build online presence.**
        
        Post your daily learning on **LinkedIn, X (Twitter), Instagram (Reels), or GitHub tagging @Databricks, @Codebasics and @indiandataclub along with #DatabricksWithIDC.**
        
        **2. Fill the submission form:** https://forms.office.com/r/GT2BquTRnH 
        
- **Voice Session: Let’s Talk Databricks**
    
    Time: 7:00 pm
    Event Link: [Let’s Talk Databricks](https://discord.gg/eWK9nkhe?event=1459429392882077707)
    
    Join Here: [IDC-Voice](https://discord.com/channels/1298526897788944474/1298526898233671743)
    

---

### **DAY 3 (11/01/26) – PySpark Transformations Deep Dive**

### Learn:

- PySpark vs Pandas comparison
- Joins (inner, left, right, outer)
- Window functions (running totals, rankings)
- User-Defined Functions (UDFs)

### 🛠️ Tasks:

1. Load full e-commerce dataset
2. Perform complex joins
3. Calculate running totals with window functions
4. Create derived features

![3.png](attachment:ea50dc35-3636-4249-ab23-51d88aa5e2b0:3.png)

### 📝 Practice:

```python
from pyspark.sql import functions as F
from pyspark.sql.window import Window

# Top 5 products by revenue
revenue = events.filter(F.col("event_type") == "purchase") \
    .groupBy("product_id", "product_name") \
    .agg(F.sum("price").alias("revenue")) \
    .orderBy(F.desc("revenue")).limit(5)

# Running total per user
window = Window.partitionBy("user_id").orderBy("event_time")
events.withColumn("cumulative_events", F.count("*").over(window))

# Conversion rate by category
events.groupBy("category_code") \
    .pivot("event_type") \
    .count() \
    .withColumn(
        "conversion_rate",
        F.col("purchase") / F.col("view") * 100
    )
```

### 🔗 Resources:

- [Window Functions](https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql/window.html)
- [PySpark Functions API](https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql/functions.html)
- ✅ **Submit your day 3 progress using this form:**
    
    Post your daily learning on **LinkedIn, X (Twitter), Instagram (Reels), or GitHub tagging @Databricks, @Codebasics and @indiandataclub along with #DatabricksWithIDC.**
    
    **2. Fill the submission form:** https://forms.office.com/r/GT2BquTRnH ****
    
- **Join the Live session Today at 7 PM (Recording will be available)**
    
    **First Knowledge Session**
    
    Get ready for a session that lays the foundation for the [**Databricks](https://www.linkedin.com/company/databricks/)** 14-Days AI Challenge.
    
    𝗗𝗮𝘁𝗲: 11th January (Sunday)
    
    𝗧𝗶𝗺𝗲: 7:00 PM
    
    𝗦𝗽𝗲𝗮𝗸𝗲𝗿:
    
    [**Snehith Allamraju**](https://www.linkedin.com/in/snehithallamraju/)
    
    𝗣𝗹𝗮𝘁𝗳𝗼𝗿𝗺: Zoom
    
    𝗦𝗲𝘀𝘀𝗶𝗼𝗻 **Zoom** 𝗟𝗶𝗻𝗸: ‣
    
    This session will help you: 
    
    - Understand Databricks fundamentals
    - Get started with the Databricks platform
    
    ⚠️ **Note:** The session capacity is **limited to 500 participants**.
    
    If you’re unable to join due to capacity limits, **the session recording will be shared the next day**.
    
    Thanks for your understanding and support 🙌
    
    ![1767526398012.jpg](attachment:cad46284-f14e-4e5f-b904-5234ed567a85:1767526398012.jpg)
    

---

### **DAY 4 (12/01/26)– Delta Lake Introduction**

### Learn:

- What is Delta Lake?
- ACID transactions
- Schema enforcement
- Delta vs Parquet

### 🛠️ Tasks:

1. Convert CSV to Delta format
2. Create Delta tables (SQL and PySpark)
3. Test schema enforcement
4. Handle duplicate inserts

![4.png](attachment:90e2e7ca-8333-464b-837d-7aa82f71c713:4.png)

### 📝 Practice:

```python
# Convert to Delta
events.write.format("delta").mode("overwrite").save("/delta/events")

# Create managed table
events.write.format("delta").saveAsTable("events_table")

# SQL approach
spark.sql("""
    CREATE TABLE events_delta
    USING DELTA
    AS SELECT * FROM events_table
""")

# Test schema enforcement
try:
    wrong_schema = spark.createDataFrame([("a","b","c")], ["x","y","z"])
    wrong_schema.write.format("delta").mode("append").save("/delta/events")
except Exception as e:
    print(f"Schema enforcement: {e}")

```

### 🔗 Resources:

- [Delta Lake Docs](https://docs.databricks.com/delta/)
- [Delta Tutorial](https://docs.databricks.com/delta/tutorial.html)
- ✅ **Submit your day 4 progress using this form:**
    
    Post your daily learning on **LinkedIn, X (Twitter), Instagram (Reels), or GitHub tagging @Databricks, @Codebasics and @indiandataclub along with #DatabricksWithIDC.**
    
    **2. Fill the submission form:** https://forms.office.com/r/GT2BquTRnH 
    

---

## PHASE 2: DATA ENGINEERING (Days 5-8)

### **DAY 5 (13/01/26) – Delta Lake Advanced**

### Note:

If you are submitting the 5-day challenge after **14/01/26 (6 PM)**, you will **not be eligible for badges**.

All the best for the 14 Days AI Challenge, try to complete it on time!

## Learn:

- Time travel (version history)
- MERGE operations (upserts)
- OPTIMIZE & ZORDER
- VACUUM for cleanup

### 🛠️ Tasks:

1. Implement incremental MERGE
2. Query historical versions
3. Optimize tables
4. Clean old files

![5.png](attachment:5b91d1ae-a34d-4ce2-bdac-00b5555344d3:5.png)

### 📝 Practice:

```python
from delta.tables import DeltaTable

# MERGE for incremental updates
deltaTable = DeltaTable.forPath(spark, "/delta/events")
updates = spark.read.csv("/path/to/new_data.csv", header=True, inferSchema=True)

deltaTable.alias("t").merge(
    updates.alias("s"),
    "t.user_session = s.user_session AND t.event_time = s.event_time"
).whenMatchedUpdateAll() \
 .whenNotMatchedInsertAll() \
 .execute()

# Time travel
v0 = spark.read.format("delta").option("versionAsOf", 0).load("/delta/events")
yesterday = spark.read.format("delta") \
    .option("timestampAsOf", "2024-01-01").load("/delta/events")

# Optimize
spark.sql("OPTIMIZE events_table ZORDER BY (event_type, user_id)")
spark.sql("VACUUM events_table RETAIN 168 HOURS")

```

### 🔗 Resources:

- [Time Travel](https://www.databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html)
- [MERGE Guide](https://docs.databricks.com/delta/merge.html)
- YT Resources
    
     1.  [Time Travel](https://youtu.be/0t-GbCW2j24?si=IfrHAfMvLqaqUO-t)
    
    1. [MERGE operations (upserts)](https://youtu.be/xQQGhmsjHv8?si=h9GYnRh6kpD996_C)
    2. [OPTIMIZE & ZORDER](https://youtu.be/KAAyqelKKgw?si=I0-NAwXcWL3bvh8R)
    3. [VACUUM for cleanup](https://youtu.be/k1XHb0kPB4M?si=epCM0bVkgpQtbeBR)
- ✅ **Submit your day 5 progress using this form:**
    
    Note:
    
    If you are submitting the 5-day challenge after **14/01/26 (6 PM)**, you will **not be eligible for badges**.
    
    All the best for the 14 Days AI Challenge, try to complete it on time! ****
    
    **1. Share your learning and build online presence.**
    
    Post your daily learning on **LinkedIn, X (Twitter), Instagram (Reels), or GitHub tagging @Databricks, @Codebasics and @indiandataclub along with #DatabricksWithIDC.**
    
    **2. Fill the submission form:** https://forms.office.com/r/GT2BquTRnH ****
    

---

### **DAY 6 (14/01/26) – Medallion Architecture**

### Learn:

- Bronze (raw) → Silver (cleaned) → Gold (aggregated)
- Best practices for each layer
- Incremental processing patterns

### 🛠️ Tasks:

1. Design 3-layer architecture
2. Build Bronze: raw ingestion
3. Build Silver: cleaning & validation
4. Build Gold: business aggregates

![6.png](attachment:a9b792e5-373d-4780-bba7-235cd0240a79:6.png)

### 📝 Practice:

```python
# BRONZE: Raw ingestion
raw = spark.read.csv("/raw/events.csv", header=True, inferSchema=True)
raw.withColumn("ingestion_ts", F.current_timestamp()) \
   .write.format("delta").mode("overwrite").save("/delta/bronze/events")

# SILVER: Cleaned data
bronze = spark.read.format("delta").load("/delta/bronze/events")
silver = bronze.filter(F.col("price") > 0) \
    .filter(F.col("price") < 10000) \
    .dropDuplicates(["user_session", "event_time"]) \
    .withColumn("event_date", F.to_date("event_time")) \
    .withColumn("price_tier",
        F.when(F.col("price") < 10, "budget")
         .when(F.col("price") < 50, "mid")
         .otherwise("premium"))
silver.write.format("delta").mode("overwrite").save("/delta/silver/events")

# GOLD: Aggregates
silver = spark.read.format("delta").load("/delta/silver/events")
product_perf = silver.groupBy("product_id", "product_name") \
    .agg(
        F.countDistinct(F.when(F.col("event_type")=="view", "user_id")).alias("views"),
        F.countDistinct(F.when(F.col("event_type")=="purchase", "user_id")).alias("purchases"),
        F.sum(F.when(F.col("event_type")=="purchase", "price")).alias("revenue")
    ).withColumn("conversion_rate", F.col("purchases")/F.col("views")*100)
product_perf.write.format("delta").mode("overwrite").save("/delta/gold/products")

```

### 🔗 Resources:

- [Medallion Architecture](https://www.databricks.com/glossary/medallion-architecture)
- [Architecture Video](https://www.youtube.com/watch?v=njjBdmAQnR0)
- [Build a Medallion Architecture with Databricks](https://youtu.be/yy9H4mlOG6I?si=cuo_wiQtW0XpbbYU)
- [Incremental processing patterns](https://youtu.be/GjV2m8b9fNY?si=-3g7rbtbj9i3axJG)
- ✅ **Submit your day 6 progress using this form:**
    
    **1. Share your learning and build online presence.**
    
    Post your daily learning on **LinkedIn, X (Twitter), Instagram (Reels), or GitHub tagging @Databricks, @Codebasics and @indiandataclub along with #DatabricksWithIDC.**
    
    **2. Fill the submission form:** https://forms.office.com/r/GT2BquTRnH ****
    

---

### **DAY 7 (15/01/26) – Workflows & Job Orchestration**

### Learn:

- Databricks Jobs vs notebooks
- Multi-task workflows
- Parameters & scheduling
- Error handling

### 🛠️ Tasks:

1. Add parameter widgets to notebooks
2. Create multi-task job (Bronze→Silver→Gold)
3. Set up dependencies
4. Schedule execution

![7.png](attachment:4fb54613-987b-49f2-8b8a-5711c8fcd286:7.png)

### 📝 Practice:

```python
# Add widgets for parameters
dbutils.widgets.text("source_path", "/default/path")
dbutils.widgets.dropdown("layer", "bronze", ["bronze","silver","gold"])

# Use parameters
source = dbutils.widgets.get("source_path")
layer = dbutils.widgets.get("layer")

def run_layer(layer_name):
    if layer_name == "bronze":
        # Bronze logic
        pass
    elif layer_name == "silver":
        # Silver logic
        pass
    # ...

# UI: Create Job
# Task 1: bronze_layer (notebook)
# Task 2: silver_layer (depends on Task 1)
# Task 3: gold_layer (depends on Task 2)
# Schedule: Daily 2 AM

```

### 🔗 Resources:

- [Jobs Documentation](https://docs.databricks.com/jobs/)
- [Job Parameters](https://docs.databricks.com/jobs/parameters.html)
- ✅ **Submit your day 7 progress using this form:**
    
    **1. Share your learning and build online presence.**
    
    Post your daily learning on **LinkedIn, X (Twitter), Instagram (Reels), or GitHub tagging @Databricks, @Codebasics and @indiandataclub along with #DatabricksWithIDC.**
    
    **2. Fill the submission form:** https://forms.office.com/r/GT2BquTRnH ****
    

---

### **DAY 8 (16/01/26) – Unity Catalog Governance**

### **Feedback Form (Feel free to share your experience by feeling this form)**: https://forms.office.com/r/aZcUUPHUWf

### Learn:

- Catalog → Schema → Table hierarchy
- Access control (GRANT/REVOKE)
- Data lineage
- Managed vs external tables

### 🛠️ Tasks:

1. Create catalog & schemas
2. Register Delta tables
3. Set up permissions
4. Create views for controlled access

![8.png](attachment:d5821c3a-8d70-4541-a9a8-d20cb32e7968:8.png)

### 📝 Practice:

```sql
-- Create structure
CREATE CATALOG ecommerce;
USE CATALOG ecommerce;
CREATE SCHEMA bronze;
CREATE SCHEMA silver;
CREATE SCHEMA gold;

-- Register tables
CREATE TABLE bronze.events USING DELTA LOCATION '/delta/bronze/events';
CREATE TABLE silver.events USING DELTA LOCATION '/delta/silver/events';
CREATE TABLE gold.products USING DELTA LOCATION '/delta/gold/products';

-- Permissions
GRANT SELECT ON TABLE gold.products TO `analysts@company.com`;
GRANT ALL PRIVILEGES ON SCHEMA silver TO `engineers@company.com`;

-- Controlled view
CREATE VIEW gold.top_products AS
SELECT product_name, revenue, conversion_rate
FROM gold.products
WHERE purchases > 10
ORDER BY revenue DESC LIMIT 100;

```

### 🔗 Resources:

- [Unity Catalog](https://docs.databricks.com/data-governance/unity-catalog/)
- [Getting Started](https://docs.databricks.com/data-governance/unity-catalog/get-started.html)
- ✅ **Submit your day 8 progress using this form:**
    
    **1. Share your learning and build online presence.**
    
    Post your daily learning on **LinkedIn, X (Twitter), Instagram (Reels), or GitHub tagging @Databricks, @Codebasics and @indiandataclub along with #DatabricksWithIDC.**
    
    **2. Fill the submission form:** https://forms.office.com/r/GT2BquTRnH ****
    
    Important Update:
    
    If you have submitted your entry but did not receive a confirmation email, please don’t worry.
    
    There has been a high number of submissions at the same time, which is why we’re facing this issue.
    
    Please continue with your learning and submit in the same way. You may not receive the submission confirmation for now (we’re working on fixing this), but the issue will be resolved soon.
    
    Thank you for your patience!
    

---

## PHASE 3: ADVANCED ANALYTICS (Days 9-11)

### **DAY 9 (17/01/26) – SQL Analytics & Dashboards**

### Learn:

- SQL warehouses
- Complex analytical queries
- Dashboard creation
- Visualizations & filters

### 🛠️ Tasks:

1. Create SQL warehouse
2. Write analytical queries
3. Build dashboard: revenue trends, funnels, top products
4. Add filters & schedule refresh

![9.png](attachment:bb5178e4-39d1-4902-ae6f-3833120e4b5c:9.png)

### 📝 Practice:

```sql
-- Revenue with 7-day moving average
WITH daily AS (
  SELECT event_date, SUM(revenue) as rev
  FROM gold.products GROUP BY event_date
)
SELECT event_date, rev,
  AVG(rev) OVER (ORDER BY event_date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) as ma7
FROM daily;

-- Conversion funnel
SELECT category_code,
  SUM(views) as views,
  SUM(purchases) as purchases,
  ROUND(SUM(purchases)*100.0/SUM(views), 2) as conversion_rate
FROM gold.products
GROUP BY category_code;

-- Customer tiers
SELECT
  CASE WHEN cnt >= 10 THEN 'VIP'
       WHEN cnt >= 5 THEN 'Loyal'
       ELSE 'Regular' END as tier,
  COUNT(*) as customers,
  AVG(total_spent) as avg_ltv
FROM (SELECT user_id, COUNT(*) cnt, SUM(price) total_spent
      FROM silver.events WHERE event_type='purchase' GROUP BY user_id)
GROUP BY tier;

```

### 🔗 Resources:

- [Databricks SQL](https://docs.databricks.com/sql/)
- [Dashboards Guide](https://docs.databricks.com/dashboards/)
- ✅ **Submit your day 9 progress using this form:**
    
    **1. Share your learning and build online presence.**
    
    Post your daily learning on **LinkedIn, X (Twitter), Instagram (Reels), or GitHub tagging @Databricks, @Codebasics and @indiandataclub along with #DatabricksWithIDC.**
    
    **2. Fill the submission form:** https://forms.office.com/r/GT2BquTRnH ****
    
- **Voice Session: Let’s Talk Databricks 2**
    
    Time: 7:00 pm
    Event Link: [Let’s Talk Databricks 2](https://discord.gg/eWK9nkhe?event=1461782430607216872)
    
    Join Here: [IDC-Voice](https://discord.com/channels/1298526897788944474/1298526898233671743)
    

---

### **DAY 10 (18/01/26) – Performance Optimization**

### Note:

If you are submitting the 10-day challenge after **19/01/26 (3 PM)**, you will **not be eligible for badges**.

All the best for the 14 Days AI Challenge, try to complete it on time!

### Learn:

- Query execution plans
- Partitioning strategies
- OPTIMIZE & ZORDER
- Caching techniques

### 🛠️ Tasks:

1. Analyze query plans
2. Partition large tables
3. Apply ZORDER
4. Benchmark improvements

![10.png](attachment:3711b67c-6224-4f4c-a07d-b686cb8fab13:10.png)

### 📝 Practice:

```python
# Explain query
spark.sql("SELECT * FROM silver.events WHERE event_type='purchase'").explain(True)

# Partitioned table
spark.sql("""
  CREATE TABLE silver.events_part
  USING DELTA
  PARTITIONED BY (event_date, event_type)
  AS SELECT * FROM silver.events
""")

# Optimize
spark.sql("OPTIMIZE silver.events_part ZORDER BY (user_id, product_id)")

# Benchmark
import time
start = time.time()
spark.sql("SELECT * FROM silver.events WHERE user_id=12345").count()
print(f"Time: {time.time()-start:.2f}s")

# Cache for iterative queries
cached = spark.table("silver.events").cache()
cached.count()  # Materialize

```

### 🔗 Resources:

- [Performance Tuning](https://docs.databricks.com/performance/)
- [Optimization Guide](https://docs.databricks.com/delta/optimizations-oss.html)
- ✅ **Submit your day 10 progress using this form:**
    
    **1. Share your learning and build online presence.**
    
    Post your daily learning on **LinkedIn, X (Twitter), Instagram (Reels), or GitHub tagging @Databricks, @Codebasics and @indiandataclub along with #DatabricksWithIDC.**
    
    **2. Fill the submission form:** https://forms.office.com/r/GT2BquTRnH ****
    
- **Join the Live session Today at 4 PM (Recording will be available)**
    
    **Second Knowledge Session**
    
    Get ready for a focused session that provides complete guidance on Databricks certifications and how to prepare for them effectively.
    
    𝗗𝗮𝘁𝗲: 18th January (Sunday)
    
    𝗧𝗶𝗺𝗲: 4:00 PM
    
    𝗦𝗽𝗲𝗮𝗸𝗲𝗿:
    
    [Prudhvi Narayana](https://www.linkedin.com/in/prudhvi-narayana-bandaru-5966b5224/)
    
    𝗣𝗹𝗮𝘁𝗳𝗼𝗿𝗺: Zoom
    
    𝗦𝗲𝘀𝘀𝗶𝗼𝗻 **Zoom** 𝗟𝗶𝗻𝗸:  https://us02web.zoom.us/j/82488221482?pwd=Y9agFbZD4DH4WeCuyi08Y75TXlODd7.1
    
    This session will help you:
    
    - Understand Databricks certification paths
    - Learn how to prepare and approach the exams
    - Get practical tips from real experience
    
    ⚠️ **Note:** The session capacity is **limited to 500 participants**.
    
    If you’re unable to join due to capacity limits, **the session recording will be shared the next day**.
    
    Thanks for your understanding and support 🙌 ****
    
    ![SQL Challenge Speakers.png](attachment:9484a949-0144-4696-9578-9ca3f2571e21:SQL_Challenge_Speakers.png)
    

---

### **DAY 11 (19/01/26) – Statistical Analysis & ML Prep**

### Learn:

- Descriptive statistics
- Hypothesis testing
- A/B test design
- Feature engineering

### 🛠️ Tasks:

1. Calculate statistical summaries
2. Test hypotheses (weekday vs weekend)
3. Identify correlations
4. Engineer features for ML

![11.png](attachment:e82839a0-74cf-46ee-a5f1-9a304ccf4d61:11.png)

### 📝 Practice:

```python
# Descriptive stats
events.describe(["price"]).show()

# Hypothesis: weekday vs weekend conversion
weekday = events.withColumn("is_weekend",
    F.dayofweek("event_date").isin([1,7]))
weekday.groupBy("is_weekend", "event_type").count().show()

# Correlation
events.stat.corr("price", "conversion_rate")

# Feature engineering
features = events.withColumn("hour", F.hour("event_time")) \
    .withColumn("day_of_week", F.dayofweek("event_date")) \
    .withColumn("price_log", F.log(F.col("price")+1)) \
    .withColumn("time_since_first_view",
        F.unix_timestamp("event_time") -
        F.first("event_time").over(Window.partitionBy("user_id").orderBy("event_time")))

```

### 🔗 Resources:

- [Spark ML Guide](https://spark.apache.org/docs/latest/ml-guide.html)
- [EDA databricks](https://youtu.be/_ROAA768D8M?si=vJMieB2kq_znHZDq)
- [EDA with PySpark](https://youtu.be/LW0Hd6TA5YQ?si=jEN0qnyFkomqzDSY)
- [Learn to Use Databricks for the Full ML Lifecycle](https://youtu.be/dQD2gVPJggQ?si=_AnthTDm0ucQe3pU)
- [What is Hypothesis Testing](https://youtu.be/fb8BSFr0isg?si=B0ue4C-o4aor_RvP)
- [Simple explanation of A/B Testing](https://youtu.be/eiIhTbFP0ls?si=QMUzAefRoZXOH7Wh)
- ✅ **Submit your day 11 progress using this form:**
    
    **1. Share your learning and build online presence.**
    
    Post your daily learning on **LinkedIn, X (Twitter), Instagram (Reels), or GitHub tagging @Databricks, @Codebasics and @indiandataclub along with #DatabricksWithIDC.**
    
    **2. Fill the submission form:** https://forms.office.com/r/GT2BquTRnH ****
    

---

## PHASE 4: AI & ML (Days 12-14)

### **DAY 12 (20/01/26) – MLflow Basics**

### Learn:

- MLflow components (tracking, registry, models)
- Experiment tracking
- Model logging
- MLflow UI

### 🛠️ Tasks:

1. Train simple regression model
2. Log parameters, metrics, model
3. View in MLflow UI
4. Compare runs

![12.png](attachment:79ed263f-3dc8-44b9-8459-b591185ff7a9:12.png)

### 📝 Practice:

```python
import mlflow
import mlflow.sklearn
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split

# Prepare data
df = spark.table("gold.products").toPandas()
X = df[["views", "cart_adds"]]
y = df["purchases"]
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)

# MLflow experiment
with mlflow.start_run(run_name="linear_regression_v1"):
    # Log parameters
    mlflow.log_param("model_type", "LinearRegression")
    mlflow.log_param("test_size", 0.2)

    # Train
    model = LinearRegression()
    model.fit(X_train, y_train)

    # Evaluate
    score = model.score(X_test, y_test)
    mlflow.log_metric("r2_score", score)

    # Log model
    mlflow.sklearn.log_model(model, "model")

print(f"R² Score: {score:.4f}")

```

### 🔗 Resources:

- [MLflow Documentation](https://docs.databricks.com/mlflow/)
- [Model Registry](https://docs.databricks.com/machine-learning/manage-model-lifecycle/)
- [MLflow 3.0: AI and MLOps on Databricks](https://youtu.be/UezTglxJC88?si=Xi4pXEkhNB25Ktx5)
- [MLOps with Databricks FREE edition:](https://youtu.be/ds__AEIqUfE?si=b3EB01LmOB5suO2v)
- ✅ **Submit your day 12 progress using this form:**
    
    **1. Share your learning and build online presence.**
    
    Post your daily learning on **LinkedIn, X (Twitter), Instagram (Reels), or GitHub tagging @Databricks, @Codebasics and @indiandataclub along with #DatabricksWithIDC.**
    
    **2. Fill the submission form:** https://forms.office.com/r/GT2BquTRnH ****
    

---

### **DAY 13 (21/01/26) – Model Comparison & Feature Engineering**

### Learn:

- Training multiple models
- Hyperparameter tuning
- Feature importance
- Spark ML Pipelines

### 🛠️ Tasks:

1. Train 3 different models
2. Compare metrics in MLflow
3. Build Spark ML pipeline
4. Select best model

![13.png](attachment:c09851e9-d952-417d-94ae-b90853780891:13.png)

### 📝 Practice:

```python
from sklearn.tree import DecisionTreeRegressor
from sklearn.ensemble import RandomForestRegressor

models = {
    "linear": LinearRegression(),
    "decision_tree": DecisionTreeRegressor(max_depth=5),
    "random_forest": RandomForestRegressor(n_estimators=100)
}

for name, model in models.items():
    with mlflow.start_run(run_name=f"{name}_model"):
        mlflow.log_param("model_type", name)

        model.fit(X_train, y_train)
        score = model.score(X_test, y_test)

        mlflow.log_metric("r2_score", score)
        mlflow.sklearn.log_model(model, "model")

        print(f"{name}: R² = {score:.4f}")

# Spark ML Pipeline
from pyspark.ml import Pipeline
from pyspark.ml.feature import VectorAssembler
from pyspark.ml.regression import LinearRegression as SparkLR

assembler = VectorAssembler(inputCols=["views","cart_adds"], outputCol="features")
lr = SparkLR(featuresCol="features", labelCol="purchases")
pipeline = Pipeline(stages=[assembler, lr])

spark_df = spark.table("gold.products")
train, test = spark_df.randomSplit([0.8, 0.2])
model = pipeline.fit(train)

```

### 🔗 Resources:

- [Spark ML](https://spark.apache.org/docs/latest/ml-classification-regression.html)
- ✅ **Submit your day 13 progress using this form:**
    
    **1. Share your learning and build online presence.**
    
    Post your daily learning on **LinkedIn, X (Twitter), Instagram (Reels), or GitHub tagging @Databricks, @Codebasics and @indiandataclub along with #DatabricksWithIDC.**
    
    **2. Fill the submission form:** https://forms.office.com/r/GT2BquTRnH ****
    

---

### **DAY 14 (22/01/26)– AI-Powered Analytics: Genie & Mosaic AI**

### Learn:

- Databricks Genie (natural language → SQL)
- Mosaic AI capabilities
- Generative AI integration
- AI-assisted analysis

### 🛠️ Tasks:

1. Use Genie to query data with natural language
2. Explore Mosaic AI features
3. Build simple NLP task
4. Create AI-powered insights

![14_Days Databricks daily Challenge tasks (1).png](attachment:0c733c66-24c0-49e0-a9dc-a0c9df475d17:14_Days_Databricks_daily_Challenge_tasks_(1).png)

### 📝 Practice:

**Genie Queries:**

- "Show me total revenue by category"
- "Which products have the highest conversion rate?"
- "What's the trend of daily purchases over time?"
- "Find customers who viewed but never purchased"

**Mosaic AI Exploration:**

```python
# Simple sentiment analysis or text classification
from transformers import pipeline

# Example: Analyze product review sentiment
classifier = pipeline("sentiment-analysis")
reviews = ["This product is amazing!", "Terrible quality, waste of money"]
results = classifier(reviews)

# Log to MLflow
with mlflow.start_run(run_name="sentiment_model"):
    mlflow.log_param("model", "distilbert-sentiment")
    mlflow.log_metric("accuracy", 0.95)  # Example metric

```

### 🔗 Resources:

- [Databricks Genie](https://www.youtube.com/watch?v=naFraZ1kMi8)
- [Mosaic AI](https://docs.databricks.com/generative-ai/)
- ✅ **Submit your day 14 progress using this form:**
    
    **1. Share your learning and build online presence.**
    
    Post your daily learning on **LinkedIn, X (Twitter), Instagram (Reels), or GitHub tagging @Databricks, @Codebasics and @indiandataclub along with #DatabricksWithIDC.**
    
    **2. Fill the submission form:** https://forms.office.com/r/GT2BquTRnH ****
    

---

## 🚀 Getting Started

1. **Download Dataset:** [Kaggle Link](https://www.kaggle.com/datasets/mkechinov/ecommerce-behavior-data-from-multi-category-store)
2. **Create Account:** [Databricks Community Edition](https://www.databricks.com/try-databricks)
3. **Upload Data:** DBFS or workspace
4. **Start Day 1!**

### **Recommended Cluster:**

- Runtime: 14.0+
- Node: i3.xlarge or similar
- Workers: 4-8

---

## 📚 Additional Resources

- [Databricks Documentation](https://docs.databricks.com/)
- [PySpark API](https://spark.apache.org/docs/latest/api/python/)
- [Delta Lake](https://delta.io/)
- [MLflow](https://mlflow.org/)
- [Kaggle Dataset](https://www.kaggle.com/datasets/mkechinov/ecommerce-behavior-data-from-multi-category-store)

---

## 🏆 DAYS 15-21: CAPSTONE PROJECT WEEK

## Overview

After mastering the fundamentals, it's time to build something real. This week is completely self-directed—you choose the problem, find the dataset, and architect the solution. This is your opportunity to showcase everything you've learned and create a portfolio-worthy project.

---

### 🚀 You've Got This!

You've spent 14 days learning Databricks—now it's time to prove what you can do. Don't wait for the perfect idea. Pick something interesting, start building, and iterate. The journey from raw data to insights is where the magic happens.

**Your challenge**: Build something that makes you think, "I can't believe I made this in a week!"

**Good luck, and happy building! 🎉**