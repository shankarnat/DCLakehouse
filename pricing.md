# Salesforce Data Cloud Credit Consumption Guide

## Credit Pricing
**1 credit = $0.005** (half a penny)

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

---

## Cost Optimization Tips

1. **Use batch activation** instead of real-time when possible (160x cheaper)
2. **Leverage free Salesforce data ingestion** for CRM data
3. **Be strategic with identity resolution** - it's the most expensive operation
4. **Process unstructured data selectively** - costs add up quickly with large document sets
5. **Monitor credit usage** across all six meters to identify optimization opportunities

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

---

*Last updated: 2025*
