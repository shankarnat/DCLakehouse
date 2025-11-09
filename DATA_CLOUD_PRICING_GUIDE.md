# Salesforce Data Cloud Pricing & Metering Guide

## Table of Contents
1. [Pricing Overview](#overview)
2. [The Six Key Meters](#meters)
3. [Real-World Analogies](#analogies)
4. [Pricing Calculator Examples](#calculator)
5. [Cost Optimization Strategies](#optimization)
6. [Customer Conversation Scripts](#scripts)

---

## Pricing Overview {#overview}

### The Data Cloud Pricing Model

Data Cloud uses a **consumption-based pricing model** with multiple meters. Think of it like your **utility bills** (electricity, water, gas) - you pay for what you use.

```
Traditional Software Licensing:
├── Pay fixed price per user/year
├── Cost is predictable but often wasteful
└── Example: $100/user × 1,000 users = $100,000/year

Data Cloud Consumption Model:
├── Pay for resources consumed
├── Cost scales with actual usage
├── Example: Storage + Ingestion + Compute + Activations
└── More efficient for variable workloads
```

### Edition-Based Starting Points

```
┌────────────────────────────────────────────────────────────┐
│              Data Cloud Editions (2025)                    │
├────────────────────────────────────────────────────────────┤
│                                                            │
│  Starter Edition                                           │
│  ├── Price: ~$108,000/year                                │
│  ├── Profiles: Up to 1 million                            │
│  ├── Includes baseline of each meter                      │
│  └── Good for: Small-medium businesses, getting started   │
│                                                            │
│  Growth Edition                                            │
│  ├── Price: ~$300,000/year                                │
│  ├── Profiles: Up to 5 million                            │
│  ├── Higher baseline meters                               │
│  └── Good for: Growing enterprises, multi-channel         │
│                                                            │
│  Enterprise Edition (Custom)                               │
│  ├── Price: Custom (typically $500K+/year)                │
│  ├── Profiles: 5M+ (unlimited with add-ons)               │
│  ├── Customizable meter allocations                       │
│  └── Good for: Large enterprises, complex use cases       │
│                                                            │
└────────────────────────────────────────────────────────────┘
```

**Important**: Editions include a **baseline allocation** of each meter. Overages are charged separately.

---

## The Six Key Meters {#meters}

### 1. Data Storage 💾

**What it measures**: How much data you store in Data Cloud (DLOs + DMOs)

**Unit**: Gigabytes (GB) or Terabytes (TB)

**Typical Pricing**: ~$0.15 - $0.25 per GB per month

#### How It Works

```
Data Storage Calculation:
├── DLOs (Data Lake Objects) - Raw ingested data
├── DMOs (Data Model Objects) - Structured data
├── Metadata and indexes
└── Historical snapshots (if time travel enabled)

Example:
├── Contact DLO: 2 GB
├── Order DLO: 50 GB
├── Web Events DLO: 200 GB
├── Individual DMO: 5 GB
├── SalesOrder DMO: 25 GB
└── Total: 282 GB × $0.20/GB = $56.40/month = $676.80/year
```

#### Real-World Example

**Customer: RetailCo (E-commerce Company)**

```
Data Sources:
├── 5 million customers → 10 GB (contact info)
├── 50 million orders → 100 GB (transaction history)
├── 500 million web events → 300 GB (clickstream)
├── 2 million product reviews → 5 GB
└── Email engagement data → 20 GB

Total Storage: ~435 GB

Annual Cost: 435 GB × $0.20/GB × 12 months = $1,044/year

Per Customer Cost: $1,044 / 5M customers = $0.0002/customer/year
```

#### Memory Aid

**Analogy**: Storage is like **renting a warehouse**
- You pay for the square footage (GBs)
- The more stuff you store, the more you pay
- You pay monthly rent whether you access it or not

---

### 2. Data Ingestion 📥

**What it measures**: How much data you bring INTO Data Cloud

**Unit**: Gigabytes (GB) ingested per month

**Typical Pricing**: ~$0.05 - $0.10 per GB ingested

#### How It Works

```
Data Ingestion = Data flowing FROM sources INTO Data Cloud

Sources:
├── Salesforce CRM sync
├── Marketing Cloud sync
├── API ingestion (real-time events)
├── CSV/Parquet uploads
├── Database connectors
└── Streaming data (Kafka, etc.)

Measurement:
└── Only counts NEW or CHANGED data (not full refreshes of unchanged data)
```

#### Ingestion Patterns

```
Pattern 1: Full Refresh (Inefficient)
├── Day 1: Ingest 100 GB (all customer data)
├── Day 2: Ingest 100 GB (same data, no changes)
├── Day 3: Ingest 100 GB (same data, no changes)
└── Total: 300 GB ingested (wasteful!)

Pattern 2: Incremental (Efficient)
├── Day 1: Ingest 100 GB (initial load)
├── Day 2: Ingest 2 GB (only new/changed records)
├── Day 3: Ingest 2 GB (only new/changed records)
└── Total: 104 GB ingested (optimal!)
```

#### Real-World Example

**Customer: BankCorp (Financial Services)**

```
Monthly Ingestion:
├── CRM Contacts (incremental daily)
│   └── 10 GB initial + 0.5 GB/day × 30 days = 25 GB/month
│
├── Transaction Data (real-time streaming)
│   └── 5 million transactions/day × 500 bytes = 2.5 GB/day
│   └── 2.5 GB × 30 days = 75 GB/month
│
├── Mobile App Events (real-time)
│   └── 50 million events/day × 200 bytes = 10 GB/day
│   └── 10 GB × 30 days = 300 GB/month
│
├── Email Engagement (daily batch)
│   └── 5 GB/day × 30 days = 150 GB/month
│
└── Total Monthly Ingestion: 550 GB

Monthly Cost: 550 GB × $0.08/GB = $44/month
Annual Cost: $44 × 12 = $528/year
```

#### Memory Aid

**Analogy**: Ingestion is like **shipping/delivery charges**
- You pay each time you receive a package (data)
- Bigger packages (GBs) cost more
- Frequent deliveries add up
- Best practice: Consolidate shipments (batch instead of real-time when possible)

---

### 3. Segmentation Compute ⚙️

**What it measures**: How much computational power you use to build and refresh segments

**Unit**: Compute Hours or Processing Units

**Typical Pricing**: Included in base edition up to a limit, then ~$0.50 - $2.00 per compute hour

#### How It Works

```
Segmentation Compute is consumed when:
├── Creating a new segment
├── Refreshing an existing segment (scheduled)
├── Running calculated insights
├── Executing complex queries on large datasets
└── Real-time segment evaluation

Factors Affecting Compute:
├── Number of records processed
├── Complexity of segment criteria
├── Number of relationships traversed
├── Frequency of refresh (hourly vs. daily)
└── Calculated insights in segment logic
```

#### Compute Cost Examples

```
Simple Segment (Low Compute):
Criteria: "Individual.State = 'California'"
├── Records scanned: 5 million
├── Processing time: 2 minutes
├── Compute cost: Minimal (often included in base)

Complex Segment (High Compute):
Criteria: "Individuals who purchased products from Category = 'Beauty'
           in the last 90 days, with total spend > $500,
           who have opened at least 3 emails,
           but have NOT purchased in the last 30 days"
├── Records scanned: 5 million individuals
├── Joins: Individual → SalesOrder → OrderLineItem → Product
├── Joins: Individual → EmailEngagement
├── Processing time: 15 minutes
├── Compute cost: 0.25 compute hours × $1.00 = $0.25

Very Complex Segment (Very High Compute):
Criteria: Multi-hop graph query with calculated insights
├── Records: 50 million
├── Multiple calculated insights evaluated
├── Processing time: 2 hours
├── Compute cost: 2 compute hours × $1.00 = $2.00
```

#### Real-World Example

**Customer: HealthTech Inc. (Healthcare SaaS)**

```
Segment Portfolio:
├── 20 simple segments (refreshed daily)
│   └── 20 segments × 2 min each × 30 refreshes/month = 20 compute hours
│
├── 10 medium segments (refreshed daily)
│   └── 10 segments × 10 min each × 30 refreshes/month = 50 compute hours
│
├── 5 complex segments (refreshed hourly)
│   └── 5 segments × 20 min each × 720 refreshes/month = 1,200 compute hours
│
└── Total Monthly Compute: 1,270 hours

Baseline Included: 500 hours (in Enterprise edition)
Overage: 770 hours × $1.00 = $770/month
Annual Cost: $770 × 12 = $9,240/year

Optimization:
├── Change hourly segments to 4x/day → Reduce to 240 hours
├── Optimize complex queries → Reduce by 30%
└── New Total: ~600 hours (fits in baseline!)
```

#### Memory Aid

**Analogy**: Compute is like **electricity for running machinery**
- Simple machines (segments) use less power
- Complex machines use more power
- Running machines constantly (hourly refresh) costs more than occasionally (daily)
- You can reduce costs by optimizing machines or running them less often

---

### 4. Activation 🚀

**What it measures**: How many records/profiles you send OUT of Data Cloud to destinations

**Unit**: Activations (records sent)

**Typical Pricing**: ~$0.001 - $0.01 per activation (varies by destination)

#### How It Works

```
Activation = Sending data FROM Data Cloud TO external systems

Activation Destinations:
├── Marketing Cloud (email sends)
├── Advertising platforms (Facebook, Google Ads)
├── Salesforce Sales/Service Cloud
├── External APIs (webhooks)
├── Mobile push notifications
└── Data warehouses (reverse ETL)

What Counts as an Activation:
├── Each record sent = 1 activation
├── Example: Segment of 100K people sent to Facebook = 100K activations
├── Daily refresh = 100K activations × 30 days = 3M activations/month
```

#### Activation Pricing Tiers

```
Typical Pricing (varies by vendor/contract):
├── Tier 1: $0.001/activation (high-volume, simple destinations)
├── Tier 2: $0.005/activation (standard destinations)
└── Tier 3: $0.01/activation (complex/premium destinations)

Some activations may be INCLUDED in base edition
(e.g., Salesforce Sales/Service Cloud activations often unlimited)
```

#### Real-World Example

**Customer: TravelCo (Airline/Hospitality)**

```
Monthly Activations:

1. Email Marketing (Marketing Cloud)
   ├── Segment: Frequent Flyers (500K people)
   ├── Frequency: Weekly newsletter
   ├── Activations: 500K × 4 weeks = 2M activations/month
   ├── Cost: Often included in MC license (no charge)

2. Facebook Custom Audiences
   ├── Segment: High-Value Travelers (200K people)
   ├── Frequency: Daily refresh
   ├── Activations: 200K × 30 days = 6M activations/month
   ├── Cost: 6M × $0.005 = $30,000/month

3. Google Ads
   ├── Segment: Abandoned Bookings (50K people)
   ├── Frequency: Hourly refresh
   ├── Activations: 50K × 24 hours × 30 days = 36M activations/month
   ├── Cost: 36M × $0.005 = $180,000/month (!!)

4. Mobile App Push Notifications
   ├── Segment: Cart Abandoners (100K people)
   ├── Frequency: Event-triggered (not scheduled)
   ├── Activations: ~200K triggers/month
   ├── Cost: 200K × $0.002 = $400/month

Total Monthly Activation Cost: $210,400/month
Annual Cost: $2,524,800/year

PROBLEM: Google Ads hourly refresh is too expensive!

Optimization:
├── Change Google Ads to 2x/day (instead of hourly)
│   └── 50K × 2 × 30 = 3M activations = $15,000/month
├── Use audience expansion on ad platform (less frequent refresh)
└── New Annual Cost: ~$540,000/year (saved $2M!)
```

#### Memory Aid

**Analogy**: Activation is like **postage/shipping costs**
- Each letter (record) you send costs money
- Sending to some destinations costs more (international vs. domestic)
- Frequent mailings (daily/hourly) cost more than occasional (weekly)
- Bulk discounts may apply

**Golden Rule**: Activation frequency is the biggest cost driver!

---

### 5. Identity Resolution 🔍

**What it measures**: How many profiles you create and maintain through identity matching

**Unit**: Identity Resolution Units (IRUs) or Unified Profiles

**Typical Pricing**: Included up to a certain number of profiles, then per-profile pricing

#### How It Works

```
Identity Resolution Process:
├── Step 1: Ingest data from multiple sources
│   └── CRM Contact, Web visitor, Email subscriber
│
├── Step 2: Apply match rules (email, phone, customer ID)
│   └── Find records that represent the same person
│
├── Step 3: Create Unified Individual profile
│   └── Merge 3 source records → 1 unified profile
│
└── Step 4: Maintain as data changes
    └── New data arrives, re-evaluate matches

Identity Resolution Units (IRUs):
└── Often measured as number of Unified Profiles created/maintained
```

#### Pricing Models

```
Model 1: Included in Edition (Most Common)
├── Starter: Up to 1M unified profiles included
├── Growth: Up to 5M unified profiles included
├── Enterprise: Custom (often 10M+ included)
└── Overage: $X per 100K profiles above limit

Model 2: Per-Profile Pricing
├── $0.01 - $0.05 per unified profile per month
└── Example: 2M profiles × $0.02 = $40K/month

Model 3: Processing-Based
├── Charged based on identity resolution compute
└── Similar to segmentation compute
```

#### Real-World Example

**Customer: MediaCo (Publishing/Streaming)**

```
Data Sources & Records:
├── Website visitors: 20 million cookie IDs
├── Mobile app users: 5 million device IDs
├── Email subscribers: 8 million email addresses
├── Paid subscribers: 2 million customer IDs
├── Social media followers: 10 million social IDs
└── Total source records: 45 million

Identity Resolution:
├── Match rules applied (email, phone, customer ID)
├── Unified profiles created: 12 million unique people
│   └── (45M source records → 12M unified profiles)
│
└── Why fewer unified profiles?
    ├── Same person has cookie + email + mobile app
    ├── Many visitors never identified (anonymous)
    └── Identity resolution merged duplicates

Pricing:
Edition: Enterprise (includes up to 10M profiles)
Overage: 2M additional profiles

Cost:
├── Base edition: $500,000/year (includes 10M)
├── Overage: 2M profiles × $0.02/profile × 12 months = $480,000/year
└── Total: $980,000/year for identity resolution

Alternative: Profile Reduction Strategy
├── Exclude anonymous visitors (only known users)
├── Unified profiles reduced to 9M
├── Fits within 10M baseline → No overage!
└── Total: $500,000/year (saved $480K!)
```

#### Memory Aid

**Analogy**: Identity Resolution is like **hiring a detective agency**
- You pay them to find all records of the same person
- More people to investigate = higher cost
- Complex cases (many sources, fuzzy matching) cost more
- Ongoing maintenance (re-checking) has ongoing cost

---

### 6. Unstructured Data Storage 📎

**What it measures**: Storage for unstructured data like images, PDFs, documents, videos, audio files

**Unit**: Gigabytes (GB) or Terabytes (TB)

**Typical Pricing**: ~$0.25 - $0.40 per GB per month (higher than structured storage)

#### How It Works

```
Unstructured Data Storage = Storing non-tabular data

What Counts as Unstructured:
├── Images (JPG, PNG, GIF, etc.)
├── Documents (PDF, Word, Excel)
├── Videos (MP4, AVI, MOV)
├── Audio files (MP3, WAV)
├── Email attachments
├── Medical imaging (DICOM, X-rays, MRIs)
└── Any binary/blob data

Why Separate from Regular Storage?
├── Different storage technology (object storage vs. columnar)
├── No indexing/query optimization needed
├── Accessed less frequently (retrieval-based)
└── Often requires CDN/serving infrastructure
```

#### Storage Tiers

```
Unstructured storage often has tiers:

Hot Storage (Frequently Accessed):
├── Price: ~$0.40/GB/month
├── Use: Product images, profile photos
├── Fast retrieval (milliseconds)
└── Best for: Active content

Cold Storage (Infrequently Accessed):
├── Price: ~$0.15/GB/month
├── Use: Historical documents, archives
├── Slower retrieval (seconds to minutes)
└── Best for: Compliance, audit trails

Archive Storage (Rarely Accessed):
├── Price: ~$0.05/GB/month
├── Use: Long-term retention
├── Very slow retrieval (hours)
└── Best for: Legal hold, regulatory requirements
```

#### Real-World Example

**Customer: HealthCare Provider (Medical Records)**

```
Unstructured Data Profile:
├── Patient photos/IDs: 2 million × 500 KB = 1 TB
├── Medical imaging (X-rays, MRIs): 100,000 × 50 MB = 5 TB
├── Lab reports (PDFs): 5 million × 200 KB = 1 TB
├── Consent forms (scanned): 2 million × 1 MB = 2 TB
├── Audio recordings (telemedicine): 500,000 × 10 MB = 5 TB
└── Total Unstructured: 14 TB

Storage Strategy:
├── Hot Storage (active patients - last 2 years):
│   ├── 4 TB × $0.40/GB × 12 months
│   ├── 4,000 GB × $0.40 × 12 = $19,200/year
│
├── Cold Storage (inactive patients - 3-7 years):
│   ├── 6 TB × $0.15/GB × 12 months
│   ├── 6,000 GB × $0.15 × 12 = $10,800/year
│
└── Archive (compliance - 7+ years):
    ├── 4 TB × $0.05/GB × 12 months
    ├── 4,000 GB × $0.05 × 12 = $2,400/year

Total Unstructured Storage Cost: $32,400/year

Compare to Structured Data:
├── Patient records (structured): 100 GB
├── Cost: 100 GB × $0.20 × 12 = $240/year
└── Unstructured is 135x more expensive (but necessary!)
```

#### Real-World Example 2

**Customer: E-commerce Company (Product Catalog)**

```
Unstructured Data:
├── Product images (primary): 100,000 products × 500 KB = 50 GB
├── Product images (variants/angles): 100,000 × 5 × 300 KB = 150 GB
├── Product videos: 10,000 × 20 MB = 200 GB
├── User-generated content (reviews): 1 million × 100 KB = 100 GB
├── Product manuals (PDFs): 50,000 × 2 MB = 100 GB
└── Total: 600 GB

Storage:
├── 600 GB × $0.35/GB × 12 months = $2,520/year

Additional Costs:
├── CDN delivery for images/videos: $500/month = $6,000/year
├── Image optimization/compression: $200/month = $2,400/year
└── Total Unstructured Ecosystem: $10,920/year

Optimization:
├── Use external CDN (Cloudflare, Akamai) for delivery
├── Store only master images in Data Cloud
├── Reference URLs instead of storing duplicates
└── Optimized cost: $1,500/year (saved $9,420!)
```

#### When to Use Unstructured Storage in Data Cloud

```
Good Use Cases:
├── Customer profile photos (linked to Individual)
├── Contract documents (linked to Account)
├── Case attachments (linked to Service Cases)
├── Product images (linked to Product DMO)
└── Compliance documents with metadata

Poor Use Cases (Use external storage instead):
├── Website content delivery (use CDN)
├── Large video streaming (use Vimeo, Wistok, YouTube)
├── App assets (use S3/CloudFront directly)
└── Archival storage only (use Glacier, Azure Archive)

Why?
└── Data Cloud unstructured storage is for LINKING data to entities
    Use external storage for pure content delivery
```

#### Best Practices

```
1. Store References, Not Files (When Possible)
   ├── Bad: Store 5 GB video in Data Cloud
   ├── Good: Store video in S3, store S3 URL in Data Cloud
   └── Link URL to customer/product record

2. Lifecycle Policies
   ├── Automatically move to cold storage after 90 days
   ├── Archive after 1 year
   └── Delete after retention period

3. Compression
   ├── Compress images before upload
   ├── Use modern formats (WebP, AVIF for images)
   └── Can reduce storage by 40-60%

4. Deduplication
   ├── Same file uploaded multiple times
   ├── Store once, reference many times
   └── Can reduce storage by 20-40%
```

#### Memory Aid

**Analogy**: Unstructured Storage is like **renting a storage unit for bulky items**
- Regular storage (structured) = Filing cabinet (organized, searchable)
- Unstructured storage = Storage unit (boxes of stuff, harder to search)
- Costs more per square foot than a filing cabinet
- Use for things that don't fit in normal files (photos, videos)
- Best practice: Store only what you need to access frequently

#### Pricing Comparison

```
Storage Type Cost Comparison (per GB/month):

Structured Data (DLOs/DMOs):     $0.20
Unstructured (Hot):              $0.40  (2x more)
Unstructured (Cold):             $0.15  (0.75x)
Unstructured (Archive):          $0.05  (0.25x)

External Alternatives:
├── AWS S3 Standard:             $0.023 (10x cheaper!)
├── AWS S3 Glacier:              $0.004 (50x cheaper!)
├── Azure Blob Storage:          $0.018 (11x cheaper!)
└── Google Cloud Storage:        $0.020 (10x cheaper!)

Key Decision:
├── Use Data Cloud unstructured storage if:
│   └── You need tight integration with DMOs (link to customer/product)
│
└── Use external storage (S3, Azure) if:
    └── Pure archival or content delivery (no DMO integration needed)
```

---

## Real-World Analogies {#analogies}

### The "Running a Restaurant" Analogy

```
┌─────────────────────────────────────────────────────────┐
│         Your Data Cloud = Your Restaurant               │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  Storage = Kitchen/Warehouse Space                      │
│  ├── Pay rent for storage space ($/sq ft)              │
│  ├── More ingredients = more space needed               │
│  └── Monthly fixed cost based on space used             │
│                                                         │
│  Ingestion = Food Delivery                              │
│  ├── Pay delivery fee each time groceries arrive        │
│  ├── More frequent deliveries = higher cost             │
│  └── Bulk deliveries are more efficient                 │
│                                                         │
│  Compute = Kitchen Utilities (Gas, Electric)            │
│  ├── Pay for energy used to cook                        │
│  ├── Complex recipes = more energy                      │
│  ├── Cooking all day = higher bill than once/day        │
│  └── Can reduce by optimizing recipes, cooking less     │
│                                                         │
│  Activation = Food Delivery to Customers                │
│  ├── Pay per meal delivered                             │
│  ├── Delivering same meal hourly = very expensive!      │
│  └── Smart: Cook once, deliver to many (batch)          │
│                                                         │
│  Identity Resolution = Customer Loyalty Program         │
│  ├── Pay to maintain customer profiles                  │
│  ├── Merge duplicate accounts (same person, 2 emails)   │
│  └── More loyal customers tracked = higher cost         │
│                                                         │
│  Unstructured Storage = Walk-in Freezer/Cold Storage    │
│  ├── Pay premium for specialized storage (photos, docs) │
│  ├── Hot storage = Front refrigerator (expensive, fast) │
│  ├── Cold storage = Back freezer (cheaper, slower)      │
│  └── Archive = Off-site warehouse (cheapest, slowest)   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### The "Gym Membership" Analogy

```
Traditional CRM = Planet Fitness ($10/month unlimited)
├── Pay flat fee
├── Use as much or little as you want
└── Cost doesn't change

Data Cloud = Equinox (Luxury Gym with Usage-Based)
├── Base membership (edition)
├── Extra for personal training sessions (compute)
├── Extra for premium classes (activations)
├── Extra for locker space (storage)
└── Pay for what you use
```

---

## Pricing Calculator Examples {#calculator}

### Example 1: Small B2C E-commerce

```
Company Profile:
├── Industry: Consumer goods e-commerce
├── Customers: 500,000
├── Monthly orders: 50,000
├── Data sources: 5 (Shopify, Klaviyo, FB Ads, Google Ads, Zendesk)

Data Cloud Setup:
├── Edition: Starter ($108K/year base)
│
├── Storage:
│   ├── Customer data: 5 GB
│   ├── Order history: 20 GB
│   ├── Web events: 50 GB
│   └── Total: 75 GB × $0.20 × 12 = $180/year
│
├── Ingestion:
│   ├── Initial load: 75 GB (one-time)
│   ├── Monthly incremental: 10 GB
│   └── 10 GB × 12 months × $0.08 = $96/year
│
├── Compute:
│   ├── 10 segments, daily refresh
│   ├── Within baseline (no overage)
│   └── $0/year
│
├── Activation:
│   ├── Email (Klaviyo): 500K × 4/month = 2M (included)
│   ├── Facebook Ads: 100K × 7/month = 700K × $0.005 = $3,500/month
│   ├── Google Ads: 50K × 7/month = 350K × $0.005 = $1,750/month
│   └── Total: $5,250/month = $63,000/year
│
└── Identity Resolution:
    ├── 500K profiles (within 1M baseline)
    └── $0/year (included)

TOTAL ANNUAL COST:
├── Base Edition: $108,000
├── Storage overage: $180
├── Ingestion overage: $96
├── Compute overage: $0
├── Activation overage: $63,000
├── Identity overage: $0
└── TOTAL: $171,276/year

Per Customer: $171,276 / 500K = $0.34/customer/year
```

### Example 2: Medium B2B SaaS Company

```
Company Profile:
├── Industry: B2B SaaS (marketing automation)
├── Customers: 50,000 companies (200,000 contacts)
├── Heavy usage: Product analytics, feature flags
├── Data sources: 8 (CRM, product DB, support, billing, etc.)

Data Cloud Setup:
├── Edition: Growth ($300K/year base)
│
├── Storage:
│   ├── Contact/Account data: 10 GB
│   ├── Product usage events: 500 GB (!!)
│   ├── Support tickets: 20 GB
│   └── Total: 530 GB × $0.20 × 12 = $1,272/year
│
├── Ingestion:
│   ├── Product events (real-time): 100 GB/month
│   ├── Other sources: 20 GB/month
│   └── 120 GB × 12 × $0.08 = $1,152/year
│
├── Compute:
│   ├── 30 segments (complex product usage segments)
│   ├── Some refreshed hourly for in-app personalization
│   ├── Baseline: 200 hours/month
│   ├── Actual usage: 400 hours/month
│   ├── Overage: 200 hours × $1.00 × 12 = $2,400/year
│
├── Activation:
│   ├── Email (included): No charge
│   ├── In-app personalization API: 5M calls/month × $0.001 = $5K/month
│   ├── Reverse ETL to warehouse: 200K records × 30 × $0.002 = $12K/month
│   └── Total: $17K/month = $204,000/year
│
└── Identity Resolution:
    ├── 200K unified profiles (within 5M baseline)
    └── $0/year (included)

TOTAL ANNUAL COST:
├── Base Edition: $300,000
├── Storage overage: $1,272
├── Ingestion overage: $1,152
├── Compute overage: $2,400
├── Activation overage: $204,000
├── Identity overage: $0
└── TOTAL: $508,824/year

Per Contact: $508,824 / 200K = $2.54/contact/year
```

### Example 3: Large Enterprise Retail

```
Company Profile:
├── Industry: Omnichannel retail (online + 500 stores)
├── Customers: 20 million
├── High frequency: Daily purchases, loyalty program
├── Data sources: 15+ (POS, e-commerce, mobile app, loyalty, etc.)

Data Cloud Setup:
├── Edition: Enterprise (Custom - $800K/year base)
│
├── Storage:
│   ├── Customer profiles: 50 GB
│   ├── Transaction history: 2 TB (2,000 GB)
│   ├── Web/mobile events: 5 TB (5,000 GB)
│   ├── Loyalty data: 100 GB
│   └── Total: 7.15 TB (7,150 GB) × $0.15 × 12 = $12,870/year
│
├── Ingestion:
│   ├── Daily transaction data: 50 GB/day = 1,500 GB/month
│   ├── Real-time events: 200 GB/day = 6,000 GB/month
│   ├── Other sources: 500 GB/month
│   └── 8,000 GB/month × 12 × $0.06 = $5,760/year
│
├── Compute:
│   ├── 100+ segments, many refreshed hourly
│   ├── Baseline: 2,000 hours/month (custom contract)
│   ├── Actual: 2,500 hours/month
│   ├── Overage: 500 hours × $0.80 × 12 = $4,800/year
│
├── Activation:
│   ├── Salesforce Sales/Service Cloud: Unlimited (included)
│   ├── Marketing Cloud: 20M profiles × 20 sends/month = 400M (included in MC)
│   ├── Facebook/Instagram: 5M × 30 refreshes × $0.003 = $450K/month
│   ├── Google Ads: 3M × 30 × $0.003 = $270K/month
│   ├── Mobile push: 10M × 10/month × $0.001 = $100K/month
│   └── Total: $820K/month = $9,840,000/year (!!)
│
└── Identity Resolution:
    ├── 20M unified profiles
    ├── Baseline: 15M included
    ├── Overage: 5M × $0.015 × 12 = $900,000/year

TOTAL ANNUAL COST:
├── Base Edition: $800,000
├── Storage overage: $12,870
├── Ingestion overage: $5,760
├── Compute overage: $4,800
├── Activation overage: $9,840,000  ← BIGGEST COST!
├── Identity overage: $900,000
└── TOTAL: $11,563,430/year

Per Customer: $11.56M / 20M = $0.58/customer/year

KEY INSIGHT: Activation frequency drives 85% of cost!
Optimization: Reduce ad platform refresh to 1x/day
New activation cost: $820K/month → $30K/month
Savings: ~$9.5M/year!
```

---

## Cost Optimization Strategies {#optimization}

### 1. Storage Optimization

```
Strategy: Data Retention Policies
├── Keep only necessary historical data
├── Archive old data to cheaper storage (S3 Glacier)
├── Example: Keep 2 years of transactions, archive older
└── Savings: 30-50% storage reduction

Strategy: Data Compression
├── Use Parquet format (columnar compression)
├── Remove redundant fields in DLOs
└── Savings: 20-40% storage reduction

Strategy: Calculated Insights vs. Raw Data
├── Store aggregated insights instead of all raw events
├── Example: Store "EmailEngagementScore" instead of every email event
└── Savings: 60-80% storage reduction
```

### 2. Ingestion Optimization

```
Strategy: Incremental Instead of Full Refresh
├── Bad: Re-ingest all 1M contacts daily
├── Good: Ingest only changed contacts (1% = 10K)
└── Savings: 99% ingestion reduction

Strategy: Batch Instead of Real-Time
├── Bad: Ingest web events real-time (every second)
├── Good: Batch every 15 minutes
└── Savings: Fewer connection overhead, 20-30% reduction

Strategy: Filter at Source
├── Bad: Ingest all data, filter in Data Cloud
├── Good: Filter unnecessary data before ingestion
├── Example: Only ingest completed orders, not draft
└── Savings: 30-50% ingestion reduction
```

### 3. Compute Optimization

```
Strategy: Reduce Segment Refresh Frequency
├── Bad: Refresh all segments hourly
├── Good: Refresh only critical segments hourly, others daily
├── Example: 10 segments × 24 hours → 10 segments × 1 hour
└── Savings: 95% compute reduction

Strategy: Optimize Segment Logic
├── Bad: Complex multi-hop joins
├── Good: Pre-calculate as insights, reference insights in segment
├── Example: Calculate "HighValueCustomer__ci" daily, use in segment
└── Savings: 70-80% compute per segment

Strategy: Use Streaming Segments Wisely
├── Use streaming only for time-sensitive use cases
├── Example: Cart abandonment (yes), loyalty tier (no)
└── Savings: 50-70% compute reduction

Strategy: Consolidate Segments
├── Bad: 50 segments with similar logic
├── Good: 10 segments with dynamic parameters
└── Savings: 80% compute reduction
```

### 4. Activation Optimization

```
Strategy: Reduce Activation Frequency ⭐ BIGGEST IMPACT
├── Bad: Hourly refresh to ad platforms
├── Good: Daily refresh (24x reduction!)
├── Question: Does audience really need hourly updates?
└── Savings: 95%+ activation reduction

Strategy: Differential Activation
├── Bad: Send entire segment (1M people) daily
├── Good: Send only changes (people entering/exiting segment)
├── Example: 1M × 30 days = 30M → 50K × 30 = 1.5M
└── Savings: 95% activation reduction

Strategy: Batch Activations
├── Bad: Send to 10 destinations separately (10 × segment size)
├── Good: Some platforms allow multi-destination (1 × segment size)
└── Savings: 90% activation reduction

Strategy: Audience Size Management
├── Bad: Send entire customer base to ad platforms
├── Good: Use lookalike audiences (smaller seed, platform expands)
├── Example: 5M seed → 500K seed, let Facebook expand
└── Savings: 90% activation reduction

Strategy: Event-Triggered vs. Scheduled
├── Bad: Refresh cart abandonment segment hourly (1M × 24 = 24M)
├── Good: Event-triggered activation (only when cart abandoned) (10K)
└── Savings: 99%+ activation reduction
```

### 5. Identity Resolution Optimization

```
Strategy: Profile Exclusion
├── Exclude anonymous/unidentified visitors
├── Only create profiles for known customers
├── Example: 20M visitors → 5M known customers
└── Savings: 75% profile reduction

Strategy: Data Source Prioritization
├── Don't ingest every possible source
├── Focus on sources that improve matching
└── Savings: 20-40% profile reduction

Strategy: Match Rule Optimization
├── Use exact matches first (email, customer ID)
├── Limit fuzzy matching (name + address)
└── Savings: Faster processing, fewer false positives
```

---

## Customer Conversation Scripts {#scripts}

### Script 1: Explaining the Pricing Model

```
You: "Data Cloud pricing is based on consumption, similar to your AWS
     or Azure bill. You pay for what you use across six key areas:

     1️⃣ STRUCTURED STORAGE - like renting warehouse space for your data
     2️⃣ UNSTRUCTURED STORAGE - for images, PDFs, documents (2x cost)
     3️⃣ INGESTION - like shipping charges to receive data
     4️⃣ COMPUTE - like electricity to process your segments
     5️⃣ ACTIVATION - like postage to send data to ad platforms
     6️⃣ IDENTITY - maintaining unified customer profiles

     Most customers find that ACTIVATION is their biggest cost driver,
     especially if they're refreshing audiences to ad platforms frequently.

     The good news is we can optimize this! Let me show you how..."
```

### Script 2: Addressing "Why So Expensive?"

```
Customer: "Why does Data Cloud cost so much? Our current CDP is $50K/year."

You: "Great question. Let's break it down per customer:

     You have 2 million customers.
     Your estimated Data Cloud cost: $400K/year
     Cost per customer: $0.20/year (or $0.017/month per customer)

     That's less than 2 cents per month per customer.

     Now, let's look at the value:
     ├── Unified customer view → Reduce customer service time by 30%
     ├── Personalized experiences → Increase conversion by 15%
     ├── Churn prediction → Retain 5% more customers
     └── Better ad targeting → Reduce CAC by 20%

     If you retain just 1% more customers (20,000 people),
     and each customer is worth $100/year in LTV,
     that's $2M in additional revenue.

     ROI: $2M value / $400K cost = 5X return

     The question isn't 'why does it cost $400K?'
     It's 'what's the ROI on our customer data?'"
```

### Script 3: Activation Cost Optimization

```
Customer: "Our activation costs are $500K/year! That's insane."

You: "Let's audit your activation strategy. I see you're refreshing
     Facebook Custom Audiences hourly. Here's what's happening:

     Current state:
     ├── Segment size: 1M people
     ├── Refresh: Hourly (24 times/day)
     ├── Activations: 1M × 24 × 30 = 720M/month
     ├── Cost: 720M × $0.0007 = $504,000/month

     Question: Does your Facebook campaign really need hourly updates?

     Optimization:
     ├── Refresh: 2x/day (morning and evening)
     ├── Activations: 1M × 2 × 30 = 60M/month
     ├── Cost: 60M × $0.0007 = $42,000/month
     └── Savings: $462,000/month ($5.5M/year!)

     Additionally, Facebook has 'lookalike audiences' - you can send
     a smaller seed audience (100K) and let Facebook expand it.
     This could reduce costs by another 90%.

     New strategy:
     ├── Seed audience: 100K (high-value customers)
     ├── Refresh: Daily
     ├── Activations: 100K × 30 = 3M/month
     ├── Cost: 3M × $0.0007 = $2,100/month
     └── Total savings: 99.6% ($502K/month → $2K/month)

     The audience performance might actually IMPROVE because you're
     giving Facebook your best customers to model from!"
```

### Script 4: Storage vs. Compute Tradeoff

```
Customer: "Should we store raw events or calculate insights?"

You: "This is a classic storage vs. compute tradeoff. Let's compare:

     OPTION 1: Store all raw events
     ├── Storage: 500 GB web events
     ├── Cost: 500 GB × $0.20 × 12 = $1,200/year
     ├── Compute: Calculate insights on-demand
     ├── Cost: 50 hours/month × $1 × 12 = $600/year
     └── Total: $1,800/year

     OPTION 2: Store only calculated insights
     ├── Storage: 5 GB (pre-aggregated insights)
     ├── Cost: 5 GB × $0.20 × 12 = $12/year
     ├── Compute: Calculate daily (incremental)
     ├── Cost: 10 hours/month × $1 × 12 = $120/year
     └── Total: $132/year

     Savings: $1,668/year (93% reduction!)

     Recommendation: Calculate insights from raw events in your
     data warehouse (cheaper), then ingest only the insights
     into Data Cloud. Use Data Cloud for activation, not storage."
```

### Script 5: ROI Justification to Executive

```
Executive: "Justify the $800K/year Data Cloud investment."

You: "Let me show you the business impact:

     WITHOUT Data Cloud (current state):
     ├── Fragmented customer data across 12 systems
     ├── Marketing sends batch-and-blast emails (2% conversion)
     ├── Support agents can't see full customer history
     ├── Churn rate: 25%/year
     └── Customer acquisition cost: $150

     WITH Data Cloud:
     ├── Unified customer view (360-degree)
     ├── Personalized campaigns (5% conversion - 2.5x improvement)
     ├── Support efficiency (30% faster resolution)
     ├── Churn reduction (25% → 20% via predictive outreach)
     └── Better ad targeting (CAC: $150 → $120)

     Financial Impact (5M customers):

     1. Churn Reduction:
        ├── 5% fewer churned = 250K customers retained
        ├── 250K × $200 LTV = $50M retained revenue

     2. Marketing Efficiency:
        ├── Conversion improvement: 2% → 5%
        ├── Revenue per campaign: $1M → $2.5M
        ├── 12 campaigns/year = $18M additional revenue

     3. Support Cost Reduction:
        ├── 30% faster resolution = 30% cost savings
        ├── Support budget: $10M → $7M
        ├── Savings: $3M/year

     4. CAC Reduction:
        ├── $150 → $120 per customer
        ├── 1M new customers/year × $30 savings
        ├── Savings: $30M/year

     Total Value: $101M/year
     Investment: $0.8M/year
     ROI: 126X

     Even if we achieve just 10% of this impact, that's $10M value
     for $800K investment - still a 12X return."
```

---

## Key Takeaways

### Remember the Pricing Meters

```
🗄️  STRUCTURED STORAGE = Warehouse rent (monthly)
📎  UNSTRUCTURED STORAGE = Storage unit for bulky items (2x cost)
📥  INGESTION = Shipping charges (per delivery)
⚙️  COMPUTE = Electricity bill (usage-based)
🚀  ACTIVATION = Postage (per piece sent)
🔍  IDENTITY = Detective agency (per person tracked)
```

### Cost Optimization Priorities

```
1. ACTIVATION FREQUENCY ⭐⭐⭐ (Biggest impact)
   └── Change hourly → daily = 95%+ savings

2. INCREMENTAL INGESTION ⭐⭐ (High impact)
   └── Only ingest changes = 90%+ savings

3. SEGMENT REFRESH ⭐⭐ (High impact)
   └── Optimize frequency = 70-90% savings

4. STORAGE STRATEGY ⭐ (Medium impact)
   └── Insights over raw data = 50-80% savings

5. PROFILE MANAGEMENT ⭐ (Medium impact)
   └── Exclude anonymous = 30-50% savings
```

### Questions to Ask Customers

```
1. "How often do your ad campaigns really need audience updates?"
   → Drives activation costs

2. "How much historical data do you need accessible?"
   → Drives storage costs

3. "Which segments are time-sensitive vs. strategic?"
   → Drives compute costs

4. "Do you need profiles for anonymous visitors?"
   → Drives identity costs

5. "Can you calculate insights upstream (in your warehouse)?"
   → Reduces storage + compute costs
```

### The Golden Rule

**"Frequency is the enemy of cost optimization."**

- Hourly segment refreshes cost 24X more than daily
- Daily activations cost 30X more than monthly
- Real-time ingestion costs more than batch

**Always ask**: "How often do we REALLY need this updated?"

---

## Quick Reference Card

```
┌────────────────────────────────────────────────────────┐
│         DATA CLOUD PRICING CHEAT SHEET                 │
├────────────────────────────────────────────────────────┤
│                                                        │
│  💾 Structured Storage: ~$0.20/GB/month               │
│  📎 Unstructured Storage: ~$0.35/GB/month (hot)       │
│  📥 Ingestion: ~$0.08/GB                              │
│  ⚙️ Compute: ~$1.00/hour                              │
│  🚀 Activation: ~$0.005/activation                    │
│  🔍 Identity: Included in edition (up to limit)       │
│                                                        │
│  🎯 Biggest Cost Driver: ACTIVATION FREQUENCY          │
│  💡 Easiest Win: Daily instead of hourly refreshes     │
│  📊 Typical Range: $0.20 - $2.00 per customer/year    │
│                                                        │
│  ⚠️  Red Flags:                                        │
│  ├── Hourly activations to ad platforms               │
│  ├── Full refresh instead of incremental              │
│  ├── Storing all raw events instead of insights       │
│  ├── Storing large files in Data Cloud (use S3)       │
│  └── Profiles for anonymous/unidentified users        │
│                                                        │
└────────────────────────────────────────────────────────┘
```

This guide should help you understand, remember, and explain Data Cloud pricing to customers with confidence!
