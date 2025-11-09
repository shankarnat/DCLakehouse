# Salesforce Data Cloud Credit Consumption Guide

## Credit Pricing
**1 credit = $0.005** (half a penny)

---

## Data Cloud Editions

### Starter Edition
- **Price**: ~$12,000/year
- **Credits Included**: 250,000 credits/month (3M credits/year)
- **Storage**: 10 GB baseline
- **Best For**: Small deployments, testing, proof-of-concept

### Growth Edition
- **Price**: ~$50,000/year
- **Credits Included**: 1,250,000 credits/month (15M credits/year)
- **Storage**: 50 GB baseline
- **Best For**: Mid-size deployments, growing data needs

### Enterprise Edition
- **Price**: ~$100,000/year
- **Credits Included**: 5,000,000 credits/month (60M credits/year)
- **Storage**: 100 GB baseline
- **Best For**: Large-scale enterprise deployments

### Pro Edition
- **Price**: Custom pricing
- **Credits Included**: Custom credit allocation
- **Storage**: Custom storage allocation
- **Best For**: Very large enterprises with complex requirements

### Einstein 1 Edition (Data Cloud Included)
- **Data Cloud bundled** with Sales/Service Cloud
- Credits and storage vary by package
- Unified pricing across Salesforce platform

**Additional Credit Packs:**
- Available for all editions when baseline credits are exhausted
- Purchased in increments (typically 1M credits or more)
- Same $0.005 per credit pricing

### Edition Usage Examples

**Starter Edition (250K credits/month):**
```
With 250,000 credits you could:
- Ingest 125M rows (batch) OR
- Run identity resolution on 2.5M rows OR
- Activate 25M rows (batch) OR
- Process 4.2 GB of unstructured data
```

**Growth Edition (1.25M credits/month):**
```
With 1,250,000 credits you could:
- Ingest 625M rows (batch) OR
- Run identity resolution on 12.5M rows OR
- Activate 125M rows (batch) OR
- Process 21 GB of unstructured data
```

**Enterprise Edition (5M credits/month):**
```
With 5,000,000 credits you could:
- Ingest 2.5B rows (batch) OR
- Run identity resolution on 50M rows OR
- Activate 500M rows (batch) OR
- Process 83 GB of unstructured data
```

**Mixed Usage Example (Enterprise):**
```
Typical monthly usage might include:
- Ingest 500M rows (batch): 1,000,000 credits
- Identity resolution 20M rows: 2,000,000 credits
- Calculated insights 100M rows: 1,500 credits
- Batch activation 200M rows: 2,000 credits
- Queries and segmentation: ~500,000 credits
Total: 3,503,500 credits (fits within 5M allocation)
```

---

## The Six Meters

### METER 1: DATA INGESTION
- **Batch ingestion**: 2,000 credits per 1M rows
- **Streaming ingestion**: Higher rate (varies)
- **FREE** for Salesforce sources (Sales Cloud, Service Cloud, Marketing Cloud, Commerce Cloud)

**Example**: Ingest 10M customer records (batch)
```
Credits: 10M × (2,000 / 1M) = 20,000 credits
Cost: 20,000 × $0.005 = $100
If from Salesforce CRM: FREE
```

---

### METER 2: IDENTITY RESOLUTION ⭐ MOST EXPENSIVE
- **Processing**: 100,000 credits per 1M rows
- **50x more expensive than ingestion!**

**Example**: Run identity resolution on 10M records
```
Credits: 10M × (100,000 / 1M) = 1,000,000 credits
Cost: 1,000,000 × $0.005 = $5,000
```

---

### METER 3: CALCULATED INSIGHTS
- **Processing**: 15 credits per 1M rows
- Very cheap compared to identity resolution

---

### METER 4: SEGMENTATION
- **Query processing**: Varies by complexity
- Now included (no separate add-on needed as of Sept 2025)

---

### METER 5: ACTIVATION
- **Batch activation**: 10 credits per 1M rows
- **Real-time streaming**: 1,600 credits per 1M rows
- **160x difference between batch and real-time!**

**Example**: Activate 5M profiles to Facebook daily for 30 days

**Batch method:**
```
Credits: 5M × 30 days × (10 / 1M) = 1,500 credits
Cost: 1,500 × $0.005 = $7.50/month
```

**Real-time streaming method:**
```
Credits: 5M × 30 × (1,600 / 1M) = 240,000 credits
Cost: 240,000 × $0.005 = $1,200/month
160x more expensive!
```

---

### METER 6: UNSTRUCTURED PROCESSING
- **AI/Vector processing**: 60 credits per 1 MB processed
- **Use case**: PDFs, documents for Agentforce/Einstein AI
- **One-time processing cost** (not recurring storage)

**Example**: Process 100 GB of PDFs for AI
```
100 GB = 100,000 MB
Credits: 100,000 MB × 60 = 6,000,000 credits
Cost: 6,000,000 × $0.005 = $30,000
This is a ONE-TIME processing cost (not monthly)
```

---

## Data Cloud Queries
- **Query processing**: Uses credits based on compute resources and data scanned
- **Data Cloud Query API**: Credits consumed based on query complexity
- **SQL queries via Data Cloud API**: Variable credit consumption
- Queries used for segmentation, insights, and data exploration
- More complex queries = more credits consumed

**Query Credit Consumption Factors:**
- Volume of data scanned
- Query complexity (joins, aggregations, filters)
- Compute resources required
- Result set size

---

## Storage (Separate from Credits)
- **Pricing**: Flat rate per TB/month (exact rate not public)
- Baseline storage included with edition
- Overage charged separately from credit consumption

---

## Key Insights 💡

| Insight | Details |
|---------|---------|
| **Identity Resolution Cost** | 50x more expensive than ingestion |
| **Real-time vs Batch Activation** | Real-time is 160x more expensive than batch |
| **Unstructured Processing** | Can be very expensive for large document sets |
| **Salesforce-Native Data** | Ingestion is now FREE (as of Sept 2025) |
| **Unified Credits** | Use credits for any operation across all meters |
| **Query Costs** | Variable based on data scanned and complexity |
| **Edition Choice Matters** | Starter (250K/mo) vs Enterprise (5M/mo) = 20x difference |
| **Credit Overage** | Purchase additional packs at $0.005/credit when needed |

---

## Cost Optimization Tips

1. **Use batch activation** instead of real-time when possible (160x cheaper)
2. **Leverage free Salesforce data ingestion** for CRM data
3. **Be strategic with identity resolution** - it's the most expensive operation
4. **Process unstructured data selectively** - costs add up quickly with large document sets
5. **Optimize queries** - use filters, limit columns, and avoid scanning unnecessary data
6. **Choose the right edition** - don't overpay for unused credits or underbuy and pay overage
7. **Monitor credit usage** across all six meters to identify optimization opportunities
8. **Use calculated insights** when possible - much cheaper than repeated queries

---

## Credit Rate Summary Table

| Meter | Operation | Rate | Unit |
|-------|-----------|------|------|
| Ingestion | Batch | 2,000 credits | per 1M rows |
| Ingestion | Streaming | Variable | per 1M rows |
| Ingestion | Salesforce Sources | **FREE** | - |
| Identity Resolution | Processing | 100,000 credits | per 1M rows |
| Calculated Insights | Processing | 15 credits | per 1M rows |
| Segmentation | Query Processing | Variable | by complexity |
| Activation | Batch | 10 credits | per 1M rows |
| Activation | Real-time Streaming | 1,600 credits | per 1M rows |
| Unstructured | AI/Vector Processing | 60 credits | per 1 MB |
| Queries | Data Cloud Query API | Variable | by data scanned & complexity |
| Queries | SQL via API | Variable | by compute & result size |

---

*Last updated: 2025*
