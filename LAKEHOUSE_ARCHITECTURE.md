# Lakehouse Architecture: The Evolution from Warehouses and Data Lakes

## Table of Contents
1. [The Evolution: From Warehouses and Data Lakes to Lakehouse](#evolution)
2. [Apache Spark's Role in Lakehouse](#spark-role)
3. [Delta Lake and the Lakehouse](#delta-lake)
4. [Apache Iceberg and Parquet Files](#iceberg-parquet)
5. [How It All Works Together](#integration)

---

## The Evolution: From Warehouses and Data Lakes to Lakehouse {#evolution}

### Traditional Data Warehouse
**Characteristics:**
- Structured data storage (tables with schemas)
- ACID transactions (Atomicity, Consistency, Isolation, Durability)
- High-performance SQL queries
- Business intelligence and reporting
- **Limitations:** Expensive, proprietary systems, poor support for unstructured data, difficult to scale

### Data Lake (2010s)
**Characteristics:**
- Store raw data in any format (structured, semi-structured, unstructured)
- Cost-effective object storage (S3, ADLS, GCS)
- Scalable and flexible
- Support for ML and data science workloads
- **Limitations:** No ACID transactions, no schema enforcement, poor data quality, "data swamp" problems, slower query performance

### The Lakehouse Paradigm (2020+)
**The Best of Both Worlds:**
```
Lakehouse = Data Lake (flexibility + low cost) + Data Warehouse (ACID + performance)
```

**Key Features:**
- Low-cost object storage (like data lakes)
- ACID transactions (like warehouses)
- Schema enforcement and evolution
- Direct file access (no ETL to separate systems)
- Support for BI, SQL, ML, and streaming in one platform
- Time travel and versioning
- Unified governance

---

## Apache Spark's Role in Lakehouse {#spark-role}

### Why Spark is Essential

**1. Unified Processing Engine**
```
Spark provides:
├── Batch Processing (DataFrame/SQL)
├── Streaming (Structured Streaming)
├── Machine Learning (MLlib)
├── Graph Processing (GraphX)
└── All on the same data platform
```

**2. Read/Write to Lakehouse Formats**
```python
# Reading Delta Lake
df = spark.read.format("delta").load("s3://bucket/delta-table")

# Reading Iceberg
df = spark.read.format("iceberg").load("catalog.db.table")

# Writing with ACID guarantees
df.write.format("delta").mode("overwrite").save("path")
```

**3. SQL Analytics on Data Lakes**
```python
# Register as SQL table
spark.sql("CREATE TABLE users USING delta LOCATION 's3://path'")

# Run complex SQL with ACID guarantees
spark.sql("""
    MERGE INTO target t
    USING source s ON t.id = s.id
    WHEN MATCHED THEN UPDATE SET *
    WHEN NOT MATCHED THEN INSERT *
""")
```

**4. Distributed Processing**
- Processes petabytes of data across clusters
- Optimizes query execution with Catalyst optimizer
- Handles data partitioning and pruning automatically

---

## Delta Lake and the Lakehouse {#delta-lake}

### What is Delta Lake?

**Delta Lake is an open-source storage layer that brings ACID transactions to data lakes.**

### Architecture
```
┌─────────────────────────────────────────┐
│         Spark / Query Engines           │
├─────────────────────────────────────────┤
│           Delta Lake Layer              │
│  ┌──────────────────────────────────┐  │
│  │  Transaction Log (_delta_log/)   │  │
│  │  - JSON logs of all operations   │  │
│  │  - ACID guarantees               │  │
│  │  - Schema enforcement            │  │
│  └──────────────────────────────────┘  │
├─────────────────────────────────────────┤
│        Parquet Files (data/)            │
├─────────────────────────────────────────┤
│    Cloud Storage (S3/ADLS/GCS)          │
└─────────────────────────────────────────�────┘
```

### Key Features

**1. ACID Transactions**
```python
# Multiple writers can safely write concurrently
df1.write.format("delta").mode("append").save(path)
df2.write.format("delta").mode("append").save(path)
# No data corruption - transactions are atomic
```

**2. Time Travel (Data Versioning)**
```python
# Query historical data
df = spark.read.format("delta").option("versionAsOf", 5).load(path)

# Or by timestamp
df = spark.read.format("delta")\
    .option("timestampAsOf", "2025-01-01").load(path)

# View history
deltaTable.history().show()
```

**3. Schema Enforcement and Evolution**
```python
# Automatically rejects incompatible schemas
df_wrong_schema.write.format("delta").mode("append").save(path)
# Raises error if schema doesn't match

# But can evolve schema when needed
df_new_columns.write.format("delta")\
    .option("mergeSchema", "true")\
    .mode("append").save(path)
```

**4. Unified Streaming and Batch**
```python
# Streaming write
stream.writeStream.format("delta")\
    .option("checkpointLocation", checkpoint)\
    .start(path)

# Batch read from the same table
df = spark.read.format("delta").load(path)
```

**5. MERGE Operations (UPSERT)**
```python
from delta.tables import DeltaTable

deltaTable = DeltaTable.forPath(spark, path)
deltaTable.alias("target").merge(
    updates.alias("source"),
    "target.id = source.id"
).whenMatchedUpdateAll()\
 .whenNotMatchedInsertAll()\
 .execute()
```

### The Transaction Log Magic

The `_delta_log/` directory contains:
```
_delta_log/
├── 00000000000000000000.json  # First transaction
├── 00000000000000000001.json  # Second transaction
├── 00000000000000000010.checkpoint.parquet  # Checkpoint
└── ...
```

Each JSON file records:
- What files were added/removed
- Schema information
- Partition information
- Statistics for optimization

---

## Apache Iceberg and Parquet Files {#iceberg-parquet}

### What is Apache Iceberg?

**Iceberg is another open table format for huge analytic datasets, similar to Delta Lake but with some architectural differences.**

### Iceberg Architecture
```
┌──────────────────────────────────────────┐
│      Query Engines (Spark, Flink,       │
│      Trino, Presto, Hive)                │
├──────────────────────────────────────────┤
│         Iceberg Table Format             │
│  ┌────────────────────────────────────┐  │
│  │  Metadata Layer                    │  │
│  │  ├── Metadata files (JSON/Avro)    │  │
│  │  ├── Manifest lists                │  │
│  │  └── Manifest files                │  │
│  └────────────────────────────────────┘  │
├──────────────────────────────────────────┤
│     Data Files (Parquet/ORC/Avro)        │
├──────────────────────────────────────────┤
│    Cloud Storage (S3/ADLS/GCS)           │
└──────────────────────────────────────────┘
```

### Iceberg's Three-Level Metadata Structure

```
Metadata File (current state)
    ├── points to Manifest List
    │
Manifest List (snapshot info)
    ├── Manifest 1 (partition 1 info)
    ├── Manifest 2 (partition 2 info)
    └── Manifest 3 (partition 3 info)
    │
Manifest Files (file-level metadata)
    ├── data_file_1.parquet (stats, row count, etc.)
    ├── data_file_2.parquet
    └── data_file_3.parquet
```

### Key Iceberg Features

**1. Hidden Partitioning**
```python
# Users don't need to know about partitioning
df = spark.read.format("iceberg").load("catalog.db.table")\
    .filter("event_date = '2025-01-01'")

# Iceberg automatically prunes partitions under the hood
```

**2. Partition Evolution**
```sql
-- Change partitioning scheme without rewriting data
ALTER TABLE events
DROP PARTITION FIELD date
ADD PARTITION FIELD month(date)
```

**3. Time Travel and Snapshots**
```python
# Query by snapshot ID
df = spark.read.format("iceberg")\
    .option("snapshot-id", 12345).load(path)

# Query by timestamp
df = spark.read.format("iceberg")\
    .option("as-of-timestamp", "1609459200000").load(path)
```

**4. Multi-Engine Support**
Iceberg works with:
- Apache Spark
- Apache Flink
- Trino/Presto
- AWS Athena
- Google BigQuery
- Snowflake
- Dremio

### Why Parquet Files?

**Parquet is the columnar storage format used by both Delta Lake and Iceberg.**

#### Benefits of Parquet:

**1. Columnar Storage**
```
Row-based (CSV):           Columnar (Parquet):
id, name, age              [id]: 1,2,3,4,5...
1, Alice, 30               [name]: Alice,Bob,Charlie...
2, Bob, 25                 [age]: 30,25,35...
3, Charlie, 35
```

**Advantages:**
- Read only columns you need (I/O reduction)
- Better compression (similar values together)
- Predicate pushdown (filter at file level)

**2. Efficient Compression**
```python
# Parquet typically achieves 75-90% compression
# CSV: 1 GB → Parquet: 100-250 MB
```

**3. Schema Evolution**
- Add/remove columns without rewriting files
- Nested data structures support

**4. Statistics and Indexing**
```
Each Parquet file stores:
├── Min/Max values per column
├── Row counts
├── Null counts
└── Column statistics

→ Enables efficient data skipping
```

**5. Predicate Pushdown Example**
```python
# Query
df = spark.read.parquet("s3://data/")\
    .filter("age > 30")

# Parquet reads min/max statistics
# Skips entire files where max(age) <= 30
# Only reads relevant files → Massive speedup
```

### How Iceberg Uses Parquet

**Iceberg adds a sophisticated metadata layer on top of Parquet:**

```python
# Writing to Iceberg (using Parquet)
df.write.format("iceberg")\
    .mode("append")\
    .save("catalog.db.table")

# Behind the scenes:
# 1. Writes data to Parquet files
# 2. Creates manifest files with metadata
# 3. Updates manifest list
# 4. Creates new metadata file (atomic commit)
```

**Benefits:**
- **File-level metadata**: Track each Parquet file's stats
- **Partition pruning**: Skip irrelevant files based on metadata
- **Data compaction**: Rewrite small files into larger ones
- **Schema evolution**: Add/drop columns across thousands of files

---

## How It All Works Together {#integration}

### Complete Lakehouse Stack

```
┌─────────────────────────────────────────────────────┐
│              Analytics & ML Tools                   │
│  (Tableau, PowerBI, Jupyter, MLflow, etc.)          │
├─────────────────────────────────────────────────────┤
│            Processing Engines                       │
│  ┌──────────────────────────────────────────────┐  │
│  │  Apache Spark (primary)                      │  │
│  │  Flink, Trino, Presto, Dremio (secondary)    │  │
│  └──────────────────────────────────────────────┘  │
├─────────────────────────────────────────────────────┤
│         Table Formats (Choose One)                  │
│  ┌──────────────────┐  ┌──────────────────────┐    │
│  │   Delta Lake     │  │   Apache Iceberg     │    │
│  │  - Transaction   │  │  - Metadata layer    │    │
│  │    log           │  │  - Manifest files    │    │
│  │  - ACID          │  │  - Hidden partition  │    │
│  │  - Time travel   │  │  - Multi-engine      │    │
│  └──────────────────┘  └──────────────────────┘    │
├─────────────────────────────────────────────────────┤
│              Storage Format                         │
│  ┌──────────────────────────────────────────────┐  │
│  │           Apache Parquet                     │  │
│  │  - Columnar storage                          │  │
│  │  - Compression                               │  │
│  │  - Statistics for pruning                    │  │
│  └──────────────────────────────────────────────┘  │
├─────────────────────────────────────────────────────┤
│         Cloud Object Storage                        │
│  (AWS S3, Azure Data Lake, Google Cloud Storage)    │
└─────────────────────────────────────────────────────┘
```

### End-to-End Example

**Scenario: Real-time user events processing**

```python
# 1. Streaming ingestion with Spark
from pyspark.sql import SparkSession
from pyspark.sql.functions import *

spark = SparkSession.builder\
    .appName("Lakehouse Example")\
    .config("spark.sql.extensions",
            "io.delta.sql.DeltaSparkSessionExtension")\
    .getOrCreate()

# 2. Read streaming data (Kafka, Kinesis, etc.)
events = spark.readStream\
    .format("kafka")\
    .option("kafka.bootstrap.servers", "localhost:9092")\
    .option("subscribe", "user_events")\
    .load()

# 3. Transform data
parsed_events = events.select(
    from_json(col("value").cast("string"), schema).alias("data")
).select("data.*")

# 4. Write to Delta Lake (ACID, streaming to batch)
parsed_events.writeStream\
    .format("delta")\
    .outputMode("append")\
    .option("checkpointLocation", "/tmp/checkpoint")\
    .start("s3://lakehouse/events_table")

# 5. Batch analytics on the same table
daily_stats = spark.read.format("delta")\
    .load("s3://lakehouse/events_table")\
    .groupBy("user_id", "date")\
    .agg(
        count("*").alias("event_count"),
        countDistinct("event_type").alias("unique_events")
    )

# 6. MERGE for incremental updates
from delta.tables import DeltaTable

target = DeltaTable.forPath(spark, "s3://lakehouse/user_stats")
target.alias("t").merge(
    daily_stats.alias("s"),
    "t.user_id = s.user_id AND t.date = s.date"
).whenMatchedUpdateAll()\
 .whenNotMatchedInsertAll()\
 .execute()

# 7. Time travel for auditing
yesterday = spark.read.format("delta")\
    .option("timestampAsOf", "2025-01-08")\
    .load("s3://lakehouse/user_stats")

# 8. ML feature engineering (same platform)
from pyspark.ml.feature import VectorAssembler
from pyspark.ml.regression import LinearRegression

features = VectorAssembler(
    inputCols=["event_count", "unique_events"],
    outputCol="features"
)
lr = LinearRegression(featuresCol="features", labelCol="target")
model = lr.fit(training_data)
```

### Key Benefits of This Integration

**1. Unified Platform**
- No ETL between systems
- Single source of truth
- Consistent data governance

**2. Cost Effective**
- Use cheap object storage (S3: ~$0.023/GB/month)
- No expensive proprietary warehouse

**3. Performance**
- Parquet's columnar format → Fast queries
- Delta/Iceberg metadata → Skip irrelevant files
- Spark's optimizer → Efficient execution

**4. Flexibility**
- Support all data types (structured, semi-structured, unstructured)
- BI, SQL, ML, streaming on same data
- Schema evolution without downtime

**5. Reliability**
- ACID transactions prevent data corruption
- Time travel for recovery
- Data versioning for auditing

---

## Comparison: Delta Lake vs Iceberg

| Feature | Delta Lake | Apache Iceberg |
|---------|-----------|----------------|
| **ACID** | ✅ Yes | ✅ Yes |
| **Time Travel** | ✅ Yes | ✅ Yes |
| **Schema Evolution** | ✅ Yes | ✅ Yes |
| **Partition Evolution** | ❌ Limited | ✅ Full support |
| **Hidden Partitioning** | ❌ No | ✅ Yes |
| **Primary Engine** | Spark (Databricks optimized) | Multi-engine (Spark, Flink, Trino, etc.) |
| **Metadata Approach** | Transaction log (JSON) | Three-level metadata (Avro/JSON) |
| **Ecosystem** | Strong Databricks integration | Vendor-neutral, broad support |
| **Streaming** | Excellent (Structured Streaming) | Good (via Flink primarily) |
| **Performance** | Excellent with Databricks | Excellent with proper optimization |

---

## Conclusion

The **Lakehouse architecture** represents a paradigm shift in data platform design:

1. **Data Warehouse** provided ACID and performance but was expensive and inflexible
2. **Data Lake** provided flexibility and low cost but lacked transactional guarantees
3. **Lakehouse** combines both: low-cost storage + ACID transactions + unified analytics

**Key Technologies:**
- **Apache Spark**: The processing engine that makes it all work
- **Delta Lake/Iceberg**: Table formats that add ACID and metadata management
- **Parquet**: Columnar storage format for efficient queries and compression
- **Cloud Storage**: Cheap, scalable object storage (S3, ADLS, GCS)

**The Result:**
A single platform for BI, SQL analytics, machine learning, and real-time streaming with:
- Low cost (object storage)
- High performance (columnar format + metadata)
- Data quality (ACID + schema enforcement)
- Flexibility (support all data types and workloads)

This is why the lakehouse has become the modern standard for data platforms at companies like Uber, Netflix, Apple, and thousands of others.
