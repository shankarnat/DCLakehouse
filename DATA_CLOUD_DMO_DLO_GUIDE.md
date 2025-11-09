# Salesforce Data Cloud: DMOs, DLOs, and Calculated Insights Guide

## Table of Contents
1. [Data Cloud Architecture Overview](#architecture)
2. [Data Lake Objects (DLOs)](#dlo)
3. [Data Model Objects (DMOs)](#dmo)
4. [Standard DMOs vs Custom DMOs](#standard-vs-custom)
5. [Custom Fields and Relationships](#fields-relationships)
6. [Calculated Insights](#calculated-insights)
7. [The Complete Data Flow](#data-flow)
8. [Best Practices](#best-practices)

---

## Data Cloud Architecture Overview {#architecture}

### The Three-Layer Architecture

```
┌─────────────────────────────────────────────────────┐
│         Layer 3: Activation & Analytics             │
│  (Segments, Calculated Insights, Predictions)       │
├─────────────────────────────────────────────────────┤
│         Layer 2: Data Model (DMOs)                  │
│  (Unified Profile, Standard/Custom DMOs)            │
├─────────────────────────────────────────────────────┤
│         Layer 1: Data Lake (DLOs)                   │
│  (Raw ingested data from sources)                   │
├─────────────────────────────────────────────────────┤
│         Layer 0: Data Sources                       │
│  (Salesforce, Marketing Cloud, External Systems)    │
└─────────────────────────────────────────────────────┘
```

### Key Concept: The Data Journey

```
Source Data → Ingestion → DLO → Mapping → DMO → Identity Resolution
                                                    ↓
                                            Unified Profile
                                                    ↓
                                    Calculated Insights & Segments
                                                    ↓
                                            Activation
```

---

## Data Lake Objects (DLOs) {#dlo}

### What are DLOs?

**Data Lake Objects (DLOs)** are the **first landing zone** for data ingested into Data Cloud. They represent the **raw, unprocessed data** from your sources.

### Characteristics of DLOs

```
DLO Characteristics:
├── Raw data format (as received from source)
├── No transformations applied
├── Store all fields from the source
├── Not yet part of the Data Model
├── Used for mapping to DMOs
└── Stored in Data Cloud's data lake
```

### How DLOs are Created

#### 1. **Through Connectors (Automatic)**

When you connect a data source, Data Cloud automatically creates DLOs:

```
Source System              Connector              DLO Created
────────────────          ──────────           ─────────────
Salesforce CRM      →     Salesforce          →    Account (DLO)
                          Connector                 Contact (DLO)
                                                   Opportunity (DLO)

Marketing Cloud     →     Marketing Cloud     →    Subscriber (DLO)
                          Connector                 EmailSend (DLO)

Amazon S3 (CSV)     →     Cloud Storage       →    CustomerData (DLO)
                          Connector
```

**Example Setup:**
```
1. Go to Data Cloud Setup
2. Select "Data Streams" → "New"
3. Choose connector type (Salesforce, Marketing Cloud, etc.)
4. Configure connection settings
5. Select objects to sync
6. DLOs are automatically created for each object
```

#### 2. **Through API/SDK (Programmatic)**

```json
// Ingestion API example
POST /api/v1/ingest/sources/{sourceApiName}/events

{
  "data": [
    {
      "customerId": "12345",
      "email": "customer@example.com",
      "purchaseAmount": 150.00,
      "purchaseDate": "2025-01-09T10:30:00Z"
    }
  ]
}

// This creates/appends to a DLO
```

#### 3. **Through Cloud Storage (CSV/Parquet)**

```
S3 Bucket Setup:
s3://my-bucket/customer-data/
    ├── customers_2025_01_01.csv
    ├── customers_2025_01_02.csv
    └── customers_2025_01_03.csv

Configuration:
├── Bucket: my-bucket
├── Path: customer-data/
├── File format: CSV
├── Schema: Auto-detect or Manual
└── Result: Creates CustomerData DLO
```

### DLO Structure Example

**Source: Salesforce Contact**
```
DLO Name: Contact_SFDC
Fields (as received from source):
├── Id (String)
├── FirstName (String)
├── LastName (String)
├── Email (String)
├── Phone (String)
├── AccountId (String)
├── CreatedDate (DateTime)
└── LastModifiedDate (DateTime)

Metadata:
├── Source: Salesforce Production
├── Last Refreshed: 2025-01-09 10:30:00
├── Record Count: 1,250,000
└── Refresh Frequency: Every 6 hours
```

### DLO Refresh Patterns

```
Refresh Types:
├── Full Refresh: Complete data replacement
│   └── Use case: Small datasets, historical snapshots
├── Incremental: Only new/changed records
│   └── Use case: Large datasets, real-time updates
└── Upsert: Insert new + update existing
    └── Use case: Maintaining current state
```

---

## Data Model Objects (DMOs) {#dmo}

### What are DMOs?

**Data Model Objects (DMOs)** are the **structured, business-ready representations** of your data. They are created by **mapping fields from DLOs** and form the foundation for segmentation and insights.

### Why DMOs Matter

```
DLOs (Raw Data)              DMOs (Business Model)
────────────────            ────────────────────
Multiple sources       →    Unified structure
No relationships       →    Related objects
Source-specific schema →    Standardized schema
Not identity-resolved  →    Linked to profiles
```

### DMO Creation Process

```
Step 1: Create DLO (data ingestion)
   ↓
Step 2: Create/Select DMO
   ↓
Step 3: Map DLO fields to DMO fields
   ↓
Step 4: Configure relationships
   ↓
Step 5: Set up identity resolution (for profile objects)
   ↓
Step 6: Publish and materialize
```

---

## Standard DMOs vs Custom DMOs {#standard-vs-custom}

### Standard DMOs (Pre-built by Salesforce)

**Standard DMOs** are **pre-configured objects** provided by Salesforce that follow common data models.

#### Common Standard DMOs

```
Individual (Person/Contact)
├── Purpose: Represents a person
├── Key Fields:
│   ├── First Name
│   ├── Last Name
│   ├── Email Address
│   ├── Phone Number
│   ├── Birth Date
│   └── Mailing Address
└── Use Cases: B2C customers, contacts, subscribers

Unified Individual
├── Purpose: Identity-resolved profile view
├── Contains: All Individual records merged by identity
└── Auto-created from Individual DMO

Sales Order
├── Purpose: Customer purchase transactions
├── Key Fields:
│   ├── Order Number
│   ├── Order Date
│   ├── Total Amount
│   ├── Status
│   └── Currency
└── Relationship: Links to Individual/Account

Product
├── Purpose: Products/services catalog
├── Key Fields:
│   ├── Product Name
│   ├── SKU
│   ├── Category
│   ├── Price
│   └── Description

Email Engagement
├── Purpose: Email marketing interactions
├── Key Fields:
│   ├── Email ID
│   ├── Send Date
│   ├── Open Date
│   ├── Click Date
│   └── Bounce Status
└── Relationship: Links to Individual

Web Engagement
├── Purpose: Website/app interactions
├── Key Fields:
│   ├── Session ID
│   ├── Page URL
│   ├── Event Type (page view, click, etc.)
│   ├── Timestamp
│   └── Duration
```

#### Creating with Standard DMOs

```
UI Steps:
1. Data Cloud Setup → Data Model
2. Click "New" → "Data Model Object"
3. Select Category: "Individual" or "Engagement" etc.
4. Choose Standard DMO Template
5. Map source DLO fields to standard DMO fields
6. Configure relationships
7. Publish

Example Mapping:
DLO Field                →    Standard DMO Field
─────────────────────        ─────────────────────
Contact.FirstName       →    Individual.FirstName
Contact.LastName        →    Individual.LastName
Contact.Email           →    Individual.EmailAddress
Contact.Phone           →    Individual.PhoneNumber
```

### Custom DMOs (User-defined)

**Custom DMOs** are objects you create to represent **business-specific data** not covered by standard DMOs.

#### When to Use Custom DMOs

```
Use Custom DMOs when:
├── Data doesn't fit standard categories
│   └── Example: Equipment rentals, subscription boxes
├── Need industry-specific objects
│   └── Example: Patient encounters (healthcare)
├── Complex business processes
│   └── Example: Multi-step approval workflows
└── Unique customer data
    └── Example: Gaming achievements, loyalty tiers
```

#### Creating Custom DMOs

**Example: Creating a "Subscription" Custom DMO**

```
Step 1: Define the Object
├── Name: Subscription
├── Category: Custom
├── Type: Profile or Engagement
└── Description: Customer subscription data

Step 2: Define Fields
├── subscription_id (Text, Primary Key)
├── individual_id (Text, Foreign Key)
├── plan_name (Text)
├── start_date (Date)
├── end_date (Date)
├── renewal_date (Date)
├── status (Text: Active, Cancelled, Expired)
├── monthly_price (Number)
└── billing_frequency (Text)

Step 3: Map from DLO
DLO: SubscriptionData
├── sub_id → subscription_id
├── customer_id → individual_id
├── plan → plan_name
├── start_dt → start_date
└── price → monthly_price

Step 4: Create Relationship
├── From: Subscription
├── To: Individual
├── Type: Many-to-One
└── Key: individual_id → Individual.Id
```

#### Custom DMO UI Configuration

```
Data Cloud Setup:
1. Navigate to: Data Model
2. Click: "New Data Model Object"
3. Select: "Custom"
4. Fill out:
   ├── Object Name: Subscription
   ├── API Name: Subscription__dlm
   ├── Category: Profile Extension
   └── Description: Customer subscription records

5. Add Fields:
   ├── Click "New Field"
   ├── Field Name: subscription_id
   ├── Data Type: Text
   ├── Mark as: Primary Key
   └── Repeat for all fields

6. Create Data Stream Mapping:
   ├── Select source DLO
   ├── Map each DLO field to DMO field
   └── Set field transformations if needed

7. Publish the DMO
```

### Standard vs Custom DMO Comparison

| Aspect | Standard DMOs | Custom DMOs |
|--------|--------------|-------------|
| **Setup Time** | Quick (pre-configured) | Longer (manual setup) |
| **Fields** | Predefined standard fields | Fully customizable |
| **Identity Resolution** | Built-in for Individual | Manual configuration |
| **Relationships** | Pre-configured common ones | Define your own |
| **Use Cases** | Common CRM entities | Business-specific data |
| **Upgrades** | Salesforce updates | You maintain |
| **Best For** | Standard customer data | Unique business needs |

---

## Custom Fields and Relationships {#fields-relationships}

### Adding Custom Fields to DMOs

Even with **Standard DMOs**, you can add **custom fields** to extend functionality.

#### Custom Field Types

```
Supported Field Types:
├── Text (short and long)
├── Number (Integer, Decimal)
├── Date
├── DateTime
├── Boolean
├── Picklist (predefined values)
└── Formula (calculated fields)
```

#### Example: Adding Custom Fields to Individual DMO

```
Standard Individual DMO
├── FirstName (standard)
├── LastName (standard)
├── Email (standard)
└── [Add Custom Fields]:
    ├── LoyaltyTier__c (Picklist: Bronze, Silver, Gold, Platinum)
    ├── LifetimeValue__c (Number: Calculated)
    ├── PreferredLanguage__c (Text)
    ├── CustomerSince__c (Date)
    └── IsVIP__c (Boolean)

Mapping:
DLO Field                  →    DMO Custom Field
─────────────────────          ─────────────────
CustomerData.tier         →    Individual.LoyaltyTier__c
CustomerData.total_spend  →    Individual.LifetimeValue__c
CustomerData.lang_pref    →    Individual.PreferredLanguage__c
```

### Relationships Between DMOs

**Relationships** connect DMOs together to represent business logic and enable complex segmentation.

#### Relationship Types

```
1. One-to-Many (1:N)
   Individual → Sales Orders
   One person has many orders

2. Many-to-One (N:1)
   Sales Orders → Individual
   Many orders belong to one person

3. Many-to-Many (N:N)
   Individuals ↔ Products (through Order Items)
   People buy multiple products, products bought by multiple people
```

#### Creating Relationships

**Example: Link Sales Order to Individual**

```
UI Configuration:
1. Open DMO: SalesOrder
2. Click: "New Relationship"
3. Configure:
   ├── Relationship Name: Buyer
   ├── Related Object: Individual
   ├── Cardinality: Many-to-One
   │   (Many orders → One individual)
   ├── Source Field: SalesOrder.individual_id
   └── Target Field: Individual.Id

4. Publish

Result:
SalesOrder.Buyer → Points to Individual record
Individual.SalesOrders → Collection of related orders
```

#### Relationship Example in Practice

```
Data Structure:

Individual DMO
├── Id: "IND_001"
├── Name: "Jane Doe"
└── Email: "jane@example.com"
    │
    │ (One-to-Many Relationship)
    ├─→ SalesOrder 1
    │   ├── OrderId: "ORD_001"
    │   ├── Amount: $150
    │   └── Date: 2025-01-01
    │
    ├─→ SalesOrder 2
    │   ├── OrderId: "ORD_002"
    │   ├── Amount: $200
    │   └── Date: 2025-01-05
    │
    └─→ SalesOrder 3
        ├── OrderId: "ORD_003"
        ├── Amount: $75
        └── Date: 2025-01-08

Usage in Segmentation:
"Individuals with more than 2 orders in the last 30 days"
→ Uses Individual → SalesOrder relationship
```

### Relationship Best Practices

```
Design Principles:
├── Model real business relationships
│   └── Example: Customer → Orders, not arbitrary links
├── Use consistent key fields
│   └── Example: Always use Id fields for relationships
├── Avoid circular relationships
│   └── Can cause performance issues
└── Document relationship purpose
    └── Helps team understand data model
```

---

## Calculated Insights {#calculated-insights}

### What are Calculated Insights?

**Calculated Insights** are **derived metrics** computed from your DMO data. They become **fields on the Unified Individual** profile that can be used for segmentation and personalization.

### Types of Calculated Insights

```
1. Aggregations
   └── Sum, Count, Average, Min, Max

2. Time-based Metrics
   └── Last 30 days, Last purchase date, etc.

3. Behavioral Metrics
   └── Engagement scores, RFM (Recency, Frequency, Monetary)

4. Derived Attributes
   └── Customer segments, propensity scores
```

### How Calculated Insights Work

```
Source Data (DMOs)
    ↓
Calculation Logic (SQL-like)
    ↓
Scheduled Computation (Daily, Hourly, Real-time)
    ↓
Result stored on Unified Individual
    ↓
Available for Segmentation/Activation
```

### Creating Calculated Insights

#### Example 1: Total Purchase Amount (Last 90 Days)

```
Configuration:
├── Name: TotalPurchases90Days
├── Output Field Type: Number
├── Calculation Type: Aggregate
├── Schedule: Daily at 2 AM

Logic (SQL-like):
SELECT
    SalesOrder.individual_id,
    SUM(SalesOrder.total_amount) AS TotalPurchases90Days
FROM SalesOrder
WHERE SalesOrder.order_date >= DATEADD(day, -90, CURRENT_DATE)
GROUP BY SalesOrder.individual_id

Result on Unified Individual:
Unified_Individual.TotalPurchases90Days = 1250.00
```

#### Example 2: Last Purchase Date

```
Configuration:
├── Name: LastPurchaseDate
├── Output Field Type: Date
├── Calculation Type: Aggregate (MAX)
├── Schedule: Hourly

Logic:
SELECT
    SalesOrder.individual_id,
    MAX(SalesOrder.order_date) AS LastPurchaseDate
FROM SalesOrder
GROUP BY SalesOrder.individual_id

Result:
Unified_Individual.LastPurchaseDate = 2025-01-08
```

#### Example 3: Email Engagement Score

```
Configuration:
├── Name: EmailEngagementScore
├── Output Field Type: Number
├── Calculation Type: Complex (Weighted)
├── Schedule: Daily

Logic:
SELECT
    EmailEngagement.individual_id,
    (COUNT(DISTINCT opens.email_id) * 1.0 +
     COUNT(DISTINCT clicks.email_id) * 2.0 +
     COUNT(DISTINCT sent.email_id) * 0.1) AS EmailEngagementScore
FROM EmailEngagement
WHERE EmailEngagement.send_date >= DATEADD(day, -90, CURRENT_DATE)
GROUP BY EmailEngagement.individual_id

Weighting:
├── Email Sent: 0.1 points
├── Email Opened: 1.0 points
└── Email Clicked: 2.0 points

Result:
Unified_Individual.EmailEngagementScore = 45.3
```

#### Example 4: Customer Lifetime Value (CLV)

```
Configuration:
├── Name: CustomerLifetimeValue
├── Output Field Type: Number (Currency)
├── Calculation Type: Aggregate (SUM)
├── Schedule: Daily

Logic:
SELECT
    SalesOrder.individual_id,
    SUM(SalesOrder.total_amount) AS CustomerLifetimeValue
FROM SalesOrder
WHERE SalesOrder.status = 'Completed'
GROUP BY SalesOrder.individual_id

Result:
Unified_Individual.CustomerLifetimeValue = 5450.75
```

#### Example 5: RFM Segmentation

```
Recency, Frequency, Monetary Model:

Configuration:
├── Name: RFM_Score
├── Output: Three separate insights
│   ├── Recency_Score (1-5)
│   ├── Frequency_Score (1-5)
│   └── Monetary_Score (1-5)

Recency (How recently they purchased):
SELECT
    individual_id,
    CASE
        WHEN DATEDIFF(day, MAX(order_date), CURRENT_DATE) <= 30 THEN 5
        WHEN DATEDIFF(day, MAX(order_date), CURRENT_DATE) <= 60 THEN 4
        WHEN DATEDIFF(day, MAX(order_date), CURRENT_DATE) <= 90 THEN 3
        WHEN DATEDIFF(day, MAX(order_date), CURRENT_DATE) <= 180 THEN 2
        ELSE 1
    END AS Recency_Score
FROM SalesOrder
GROUP BY individual_id

Frequency (How often they purchase):
SELECT
    individual_id,
    CASE
        WHEN COUNT(order_id) >= 10 THEN 5
        WHEN COUNT(order_id) >= 7 THEN 4
        WHEN COUNT(order_id) >= 4 THEN 3
        WHEN COUNT(order_id) >= 2 THEN 2
        ELSE 1
    END AS Frequency_Score
FROM SalesOrder
WHERE order_date >= DATEADD(year, -1, CURRENT_DATE)
GROUP BY individual_id

Monetary (How much they spend):
SELECT
    individual_id,
    CASE
        WHEN SUM(total_amount) >= 1000 THEN 5
        WHEN SUM(total_amount) >= 500 THEN 4
        WHEN SUM(total_amount) >= 200 THEN 3
        WHEN SUM(total_amount) >= 50 THEN 2
        ELSE 1
    END AS Monetary_Score
FROM SalesOrder
WHERE order_date >= DATEADD(year, -1, CURRENT_DATE)
GROUP BY individual_id

Combined RFM Segment:
RFM_555 = Champions (Best customers)
RFM_111 = At Risk (Need attention)
```

### Calculated Insight UI Steps

```
1. Navigate to: Data Cloud → Calculated Insights
2. Click: "New Calculated Insight"
3. Fill Details:
   ├── Name: TotalPurchases90Days
   ├── API Name: TotalPurchases90Days__ci
   ├── Description: Total purchase amount in last 90 days
   └── Category: Customer Value

4. Define Calculation:
   ├── Select Source DMO: SalesOrder
   ├── Filter: order_date in last 90 days
   ├── Aggregate: SUM(total_amount)
   └── Group By: individual_id

5. Set Schedule:
   ├── Frequency: Daily
   ├── Time: 2:00 AM
   └── Timezone: UTC

6. Map to Profile:
   ├── Target Object: Unified Individual
   ├── Field Name: TotalPurchases90Days
   └── Data Type: Number(10,2)

7. Activate and Run
```

### Using Calculated Insights in Segmentation

```
Example Segment: High-Value Recent Customers

Criteria:
├── TotalPurchases90Days > 1000
├── LastPurchaseDate within last 30 days
└── EmailEngagementScore > 40

SQL Generated:
SELECT *
FROM UnifiedIndividual
WHERE TotalPurchases90Days > 1000
  AND LastPurchaseDate >= DATEADD(day, -30, CURRENT_DATE)
  AND EmailEngagementScore > 40

Use Cases:
├── Send VIP offer email
├── Personalize website experience
└── Route to premium support queue
```

### Calculated Insight Performance Considerations

```
Best Practices:
├── Schedule during off-peak hours
│   └── Large aggregations at night
├── Use incremental calculations when possible
│   └── Don't recalculate all history daily
├── Index relationship fields
│   └── Speeds up joins
├── Limit historical lookback periods
│   └── Last 90 days vs all-time
└── Monitor calculation run times
    └── Optimize slow-running insights
```

---

## The Complete Data Flow {#data-flow}

### End-to-End Example: E-commerce Customer Journey

```
Step 1: Data Ingestion (DLO Creation)
─────────────────────────────────────
Sources → DLOs:
├── Salesforce CRM → Contact_SFDC (DLO)
├── E-commerce DB → Order_History (DLO)
├── Marketing Cloud → Email_Activity (DLO)
└── Website Analytics → Web_Events (DLO)

Step 2: Data Modeling (DMO Creation)
────────────────────────────────────
DLOs → DMOs:
├── Contact_SFDC → Individual (Standard DMO)
├── Order_History → SalesOrder (Standard DMO)
├── Email_Activity → EmailEngagement (Standard DMO)
└── Web_Events → WebEngagement (Custom DMO)

Field Mappings:
Contact_SFDC.Email → Individual.EmailAddress
Contact_SFDC.FirstName → Individual.FirstName
Order_History.order_id → SalesOrder.OrderNumber
Order_History.total → SalesOrder.TotalAmount

Step 3: Relationship Configuration
──────────────────────────────────
SalesOrder.customer_id → Individual.Id (Many-to-One)
EmailEngagement.contact_id → Individual.Id (Many-to-One)
WebEngagement.user_id → Individual.Id (Many-to-One)

Step 4: Identity Resolution
───────────────────────────
Match Rules:
├── Email (exact match)
├── Phone (normalized match)
└── Customer ID (exact match)

Result:
Multiple Individual records → Unified Individual profile

Step 5: Calculated Insights
───────────────────────────
Create Insights:
├── TotalPurchases90Days (from SalesOrder)
├── LastPurchaseDate (from SalesOrder)
├── EmailEngagementScore (from EmailEngagement)
├── WebVisitsLast30Days (from WebEngagement)
└── CustomerLifetimeValue (from SalesOrder)

Step 6: Segmentation
───────────────────
Create Segments:
├── High Value Customers
│   └── CustomerLifetimeValue > 1000
├── At Risk Customers
│   └── LastPurchaseDate > 90 days ago
│       AND EmailEngagementScore < 20
└── Engaged Shoppers
    └── WebVisitsLast30Days > 5
        AND TotalPurchases90Days > 0

Step 7: Activation
─────────────────
Use Segments:
├── Send personalized emails (Marketing Cloud)
├── Create lookalike audiences (Facebook Ads)
├── Personalize website (Real-time API)
└── Update CRM (Salesforce Sales Cloud)
```

### Visual Data Flow Diagram

```
┌──────────────────────────────────────────────────────────┐
│                   DATA SOURCES                           │
│  [Salesforce] [Marketing Cloud] [Website] [E-commerce]   │
└────────────────────┬─────────────────────────────────────┘
                     │ Ingestion
                     ↓
┌──────────────────────────────────────────────────────────┐
│                  DATA LAKE (DLOs)                        │
│  [Contact_DLO] [Email_DLO] [Order_DLO] [Web_DLO]         │
└────────────────────┬─────────────────────────────────────┘
                     │ Mapping
                     ↓
┌──────────────────────────────────────────────────────────┐
│               DATA MODEL (DMOs)                          │
│  [Individual] [SalesOrder] [EmailEngagement] [Web]       │
│         ↑                                                │
│         │ Relationships                                  │
│         └─────────────┬──────────────┐                   │
└───────────────────────┼──────────────┼───────────────────┘
                        │              │
                        ↓              ↓
┌──────────────────────────────────────────────────────────┐
│             IDENTITY RESOLUTION                          │
│  Contact1 + Email1 + Web1 → Unified Individual A         │
│  Contact2 + Email2 + Web2 → Unified Individual B         │
└────────────────────┬─────────────────────────────────────┘
                     │
                     ↓
┌──────────────────────────────────────────────────────────┐
│           CALCULATED INSIGHTS                            │
│  • TotalPurchases90Days                                  │
│  • EmailEngagementScore                                  │
│  • CustomerLifetimeValue                                 │
└────────────────────┬─────────────────────────────────────┘
                     │
                     ↓
┌──────────────────────────────────────────────────────────┐
│              SEGMENTATION                                │
│  Segment: High Value Customers (15,234 individuals)      │
└────────────────────┬─────────────────────────────────────┘
                     │
                     ↓
┌──────────────────────────────────────────────────────────┐
│               ACTIVATION                                 │
│  [Email Campaign] [Ads] [Personalization] [CRM Update]   │
└──────────────────────────────────────────────────────────┘
```

---

## Best Practices {#best-practices}

### Data Modeling Best Practices

```
1. Start with Standard DMOs
   ├── Use out-of-the-box objects when possible
   ├── Only create custom DMOs for unique needs
   └── Extend standard DMOs with custom fields

2. Plan Your Relationships
   ├── Map out data model before implementation
   ├── Identify key entities (Individuals, Orders, etc.)
   └── Define relationship cardinality correctly

3. Use Consistent Naming
   ├── DMO: CustomerOrder (not Order1, OrderData)
   ├── Fields: order_date (not date, ord_dt, orderDate)
   └── Custom fields: LoyaltyTier__c (follow conventions)

4. Optimize for Performance
   ├── Index frequently-used relationship fields
   ├── Limit DLO→DMO mappings to necessary fields
   └── Use incremental refresh for large datasets
```

### Calculated Insight Best Practices

```
1. Keep Calculations Simple
   ├── Complex logic → slower computation
   ├── Break complex insights into multiple simpler ones
   └── Use materialized views when needed

2. Schedule Wisely
   ├── Daily updates for strategic metrics (CLV)
   ├── Hourly for operational metrics (cart abandonment)
   └── Real-time for critical actions (fraud detection)

3. Test Before Production
   ├── Validate calculation logic on sample data
   ├── Compare results with known values
   └── Monitor initial runs for errors

4. Document Everything
   ├── Business definition of metric
   ├── Calculation logic and formulas
   ├── Update frequency and dependencies
   └── Use cases and owners
```

### Identity Resolution Best Practices

```
1. Use Multiple Match Rules
   ├── Email (high confidence)
   ├── Phone (medium confidence)
   ├── Customer ID (high confidence)
   └── Name + Address (low confidence)

2. Prioritize Match Rules
   ├── Exact matches first
   ├── Fuzzy matches with thresholds
   └── Avoid false positives

3. Monitor Match Rates
   ├── Track % of records matched
   ├── Identify unmatched records
   └── Refine rules based on results
```

### Common Pitfalls to Avoid

```
❌ DON'T:
├── Create custom DMOs for standard entities
│   └── Use Individual instead of custom Customer__c
├── Skip relationship configuration
│   └── Limits segmentation capabilities
├── Over-calculate insights
│   └── 100 insights running hourly = performance issues
├── Ignore data quality in DLOs
│   └── Garbage in = garbage out
└── Forget to document data model
    └── Team confusion and errors

✅ DO:
├── Leverage standard DMOs and extend them
├── Model real business relationships
├── Calculate insights on-demand when possible
├── Implement data quality checks at ingestion
└── Maintain data dictionary and documentation
```

---

## Quick Reference

### Key Terms Summary

| Term | Definition | Example |
|------|------------|---------|
| **DLO** | Data Lake Object - Raw data from sources | Contact_SFDC, OrderHistory_CSV |
| **DMO** | Data Model Object - Structured business objects | Individual, SalesOrder, EmailEngagement |
| **Standard DMO** | Pre-built DMO by Salesforce | Individual, Sales Order, Product |
| **Custom DMO** | User-created DMO | Subscription, Rental, Appointment |
| **Calculated Insight** | Derived metric on Unified Individual | TotalPurchases90Days, CLV |
| **Relationship** | Link between DMOs | SalesOrder → Individual |
| **Identity Resolution** | Merging individual records into unified profile | 3 contacts → 1 Unified Individual |

### Common Questions for Engineers

```
When discussing with engineers:

1. "What data sources do we need to connect?"
   → This determines which DLOs will be created

2. "Do we need custom DMOs or can we use standard ones?"
   → Assess if standard Individual, Order, etc. are sufficient

3. "What relationships exist between our data entities?"
   → Map out Customer→Orders, Orders→Products, etc.

4. "What metrics do we need for segmentation?"
   → These become Calculated Insights

5. "How fresh does the data need to be?"
   → Determines DLO refresh frequency and insight schedules

6. "What fields do we need for identity matching?"
   → Email, Phone, Customer ID for identity resolution

7. "Are there any custom attributes we need to track?"
   → Custom fields on standard DMOs or custom DMOs
```

---

## Conclusion

Understanding Data Cloud's architecture empowers you to:

✅ **Design effective data models** using DLOs and DMOs
✅ **Leverage standard DMOs** while extending with custom fields
✅ **Create custom DMOs** for unique business needs
✅ **Build relationships** that reflect real business logic
✅ **Calculate insights** that drive segmentation and activation
✅ **Communicate effectively** with engineers about requirements

The key is understanding the flow: **Source → DLO → DMO → Relationships → Identity → Insights → Activation**

Master these concepts and you'll be well-equipped to design and implement powerful customer data platforms in Salesforce Data Cloud!
