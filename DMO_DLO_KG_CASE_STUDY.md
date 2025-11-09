# Knowledge Graph Case Study: Building Customer 360 with DMOs, DLOs, and Relationships

## Table of Contents
1. [Introduction: The Knowledge Graph Vision](#introduction)
2. [Business Context: OmniRetail Inc.](#business-context)
3. [Data Sources and DLO Structure](#dlo-structure)
4. [DMO Architecture: Standard + Custom](#dmo-architecture)
5. [Relationship Mapping: Building the Knowledge Graph](#relationships)
6. [Affinities and Calculated Insights](#affinities)
7. [Knowledge Graph Visualization](#kg-visualization)
8. [Advanced Use Cases](#use-cases)
9. [Multi-Industry Examples](#industry-examples)

---

## Introduction: The Knowledge Graph Vision {#introduction}

### What is a Customer Knowledge Graph?

A **Customer Knowledge Graph (KG)** is a connected data model that represents:
- **Entities** (Customers, Products, Orders, Subscriptions, etc.)
- **Relationships** (purchased, subscribed to, interacted with)
- **Attributes** (demographics, preferences, behaviors)
- **Affinities** (product affinity, channel preference, brand loyalty)

```
Traditional Database:          Knowledge Graph:
┌──────────┐                   ┌─────────────┐
│ Customer │                   │   Customer  │
│   Table  │                   │   (Node)    │
└──────────┘                   └──────┬──────┘
                                      │ purchased
┌──────────┐                          ↓
│  Order   │                   ┌─────────────┐
│   Table  │                   │    Order    │──bought──→ ┌─────────┐
└──────────┘                   │   (Node)    │            │ Product │
                               └─────────────┘            │ (Node)  │
Foreign Keys                   Rich Relationships         └─────────┘
```

### Why Knowledge Graphs Matter in Data Cloud

```
Benefits:
├── 360-degree customer view
├── Discover hidden patterns and relationships
├── Personalization at scale
├── Predictive analytics and recommendations
├── Real-time decisioning
└── Cross-entity insights (customer + product + behavior)
```

---

## Business Context: OmniRetail Inc. {#business-context}

### Company Profile

**OmniRetail Inc.** is a multi-channel retail company offering:
- E-commerce platform (website and mobile app)
- Physical retail stores (150 locations)
- Subscription box service
- Loyalty program with tiered benefits
- Customer service across multiple channels

### Business Challenges

```
Challenges:
├── Fragmented customer data across 12+ systems
├── No unified view of customer journey
├── Unable to personalize across channels
├── Limited understanding of product affinities
├── Ineffective loyalty program engagement
└── Poor visibility into subscription health
```

### Data Cloud Solution: Build a Customer Knowledge Graph

```
Goals:
├── Unify customer data from all touchpoints
├── Create connected data model (KG)
├── Enable real-time personalization
├── Drive product recommendations
├── Predict churn and lifetime value
└── Optimize loyalty program
```

---

## Data Sources and DLO Structure {#dlo-structure}

### Source Systems → DLOs

```
┌─────────────────────────────────────────────────────────────┐
│                      DATA SOURCES                           │
├─────────────────────────────────────────────────────────────┤
│ 1. Salesforce CRM        → Contact, Account, Opportunity    │
│ 2. E-commerce Platform   → Orders, Cart Events, Reviews     │
│ 3. Subscription System   → Subscription Plans, Billing      │
│ 4. Loyalty Platform      → Points, Tiers, Rewards           │
│ 5. Marketing Cloud       → Email Sends, Opens, Clicks       │
│ 6. Mobile App            → App Events, Push Notifications   │
│ 7. Store POS System      → In-store Purchases, Returns      │
│ 8. Customer Service      → Cases, Chat Transcripts          │
│ 9. Product Catalog       → Products, Inventory, Attributes  │
│ 10. Web Analytics        → Page Views, Sessions, Events     │
│ 11. Social Media         → Social Posts, Sentiment          │
│ 12. Payment System       → Payment Methods, Transactions    │
└─────────────────────────────────────────────────────────────┘
```

### DLO Examples

#### DLO 1: E-commerce Orders
```
DLO Name: Ecommerce_Orders__dlo
Source: E-commerce Database (MySQL)
Connector: Cloud Storage (Daily CSV Export)
Refresh: Daily at 2 AM

Fields:
├── order_id (String) - Primary Key
├── customer_email (String)
├── customer_id (String)
├── order_date (DateTime)
├── order_total (Decimal)
├── order_status (String)
├── shipping_address_line1 (String)
├── shipping_address_city (String)
├── shipping_address_state (String)
├── shipping_address_zip (String)
├── payment_method (String)
├── discount_code (String)
├── discount_amount (Decimal)
├── tax_amount (Decimal)
├── shipping_cost (Decimal)
├── order_source (String) - web, mobile, store
└── created_timestamp (DateTime)

Sample Data:
order_id: "ORD-2025-00123"
customer_email: "sarah.johnson@email.com"
order_date: "2025-01-09T10:30:00Z"
order_total: 249.99
order_status: "shipped"
```

#### DLO 2: Subscription Service
```
DLO Name: Subscription_Data__dlo
Source: Subscription Management System (Zuora API)
Connector: API Connector
Refresh: Hourly

Fields:
├── subscription_id (String)
├── customer_email (String)
├── subscription_type (String) - beauty_box, fitness_box, gourmet_box
├── plan_name (String)
├── start_date (Date)
├── end_date (Date)
├── next_renewal_date (Date)
├── billing_frequency (String) - monthly, quarterly, annual
├── subscription_status (String) - active, paused, cancelled, expired
├── monthly_recurring_revenue (Decimal)
├── total_revenue (Decimal)
├── cancellation_date (Date)
├── cancellation_reason (String)
├── auto_renewal (Boolean)
├── payment_method_id (String)
└── last_modified (DateTime)

Sample Data:
subscription_id: "SUB-45678"
customer_email: "sarah.johnson@email.com"
subscription_type: "beauty_box"
plan_name: "Premium Beauty Monthly"
monthly_recurring_revenue: 49.99
subscription_status: "active"
```

#### DLO 3: Loyalty Program
```
DLO Name: Loyalty_Program__dlo
Source: Loyalty Platform (Custom API)
Connector: Ingestion API
Refresh: Real-time (streaming)

Fields:
├── loyalty_member_id (String)
├── customer_id (String)
├── email_address (String)
├── loyalty_tier (String) - bronze, silver, gold, platinum
├── current_points (Integer)
├── lifetime_points (Integer)
├── points_expiring_soon (Integer)
├── tier_start_date (Date)
├── tier_expiration_date (Date)
├── member_since (Date)
├── tier_upgrade_threshold (Integer)
├── preferred_rewards_category (String)
├── rewards_redeemed_count (Integer)
├── last_activity_date (Date)
└── marketing_opt_in (Boolean)

Sample Data:
loyalty_member_id: "LOY-789012"
email_address: "sarah.johnson@email.com"
loyalty_tier: "gold"
current_points: 12500
lifetime_points: 45300
```

#### DLO 4: Product Catalog
```
DLO Name: Product_Catalog__dlo
Source: Product Master Data (PostgreSQL)
Connector: Database Connector
Refresh: Daily

Fields:
├── product_id (String)
├── sku (String)
├── product_name (String)
├── product_category (String)
├── product_subcategory (String)
├── brand (String)
├── price (Decimal)
├── sale_price (Decimal)
├── cost (Decimal)
├── inventory_quantity (Integer)
├── product_description (Text)
├── product_attributes (JSON) - color, size, material, etc.
├── is_subscription_eligible (Boolean)
├── sustainability_score (Integer)
├── average_rating (Decimal)
├── review_count (Integer)
└── launch_date (Date)

Sample Data:
product_id: "PROD-55123"
sku: "BEAUTY-LIP-001"
product_name: "Premium Matte Lipstick - Ruby Red"
product_category: "Beauty"
brand: "LuxeBeauty"
price: 24.99
```

#### DLO 5: Customer Service Interactions
```
DLO Name: Service_Cases__dlo
Source: Salesforce Service Cloud
Connector: Salesforce Connector
Refresh: Every 6 hours

Fields:
├── case_id (String)
├── case_number (String)
├── contact_id (String)
├── customer_email (String)
├── case_type (String) - inquiry, complaint, return, technical
├── case_subject (String)
├── case_description (Text)
├── case_status (String) - new, in_progress, resolved, closed
├── priority (String) - low, medium, high, critical
├── created_date (DateTime)
├── closed_date (DateTime)
├── resolution_time_hours (Decimal)
├── first_response_time_hours (Decimal)
├── channel (String) - phone, email, chat, social
├── sentiment_score (Decimal) - -1.0 to 1.0
└── csat_score (Integer) - 1 to 5

Sample Data:
case_id: "500XX000001ABC"
customer_email: "sarah.johnson@email.com"
case_type: "inquiry"
sentiment_score: 0.8
csat_score: 5
```

---

## DMO Architecture: Standard + Custom {#dmo-architecture}

### Knowledge Graph Entity Map

```
┌──────────────────────────────────────────────────────────────┐
│                    CORE ENTITIES (DMOs)                      │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌─────────────┐                                             │
│  │ Individual  │ ← Standard DMO + Custom Fields              │
│  │  (Person)   │                                             │
│  └──────┬──────┘                                             │
│         │                                                    │
│    Relationships to:                                         │
│         ├─→ SalesOrder (Standard DMO)                        │
│         ├─→ Subscription (Custom DMO)                        │
│         ├─→ LoyaltyMember (Custom DMO)                       │
│         ├─→ ProductReview (Custom DMO)                       │
│         ├─→ ServiceCase (Standard DMO + Custom)              │
│         ├─→ EmailEngagement (Standard DMO)                   │
│         ├─→ WebEngagement (Standard DMO)                     │
│         └─→ PaymentMethod (Custom DMO)                       │
│                                                              │
│  ┌─────────────┐                                             │
│  │   Product   │ ← Standard DMO + Custom Fields              │
│  └──────┬──────┘                                             │
│         │                                                    │
│    Relationships:                                            │
│         ├─→ ProductCategory (Custom DMO)                     │
│         ├─→ Brand (Custom DMO)                               │
│         └─→ ProductAffinityScore (Calculated)                │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

### DMO 1: Individual (Standard DMO with Custom Fields)

```
DMO Name: Individual
Type: Standard DMO (Person Profile)
Source DLO Mappings:
  ├── Contact_SFDC → Individual
  ├── Ecommerce_Orders__dlo → Individual (email match)
  └── Loyalty_Program__dlo → Individual (email match)

STANDARD FIELDS:
├── Id (String) - System generated
├── FirstName (String)
├── LastName (String)
├── EmailAddress (String)
├── PhoneNumber (String)
├── MobileNumber (String)
├── BirthDate (Date)
├── MailingStreet (String)
├── MailingCity (String)
├── MailingState (String)
├── MailingPostalCode (String)
├── MailingCountry (String)
└── Gender (String)

CUSTOM FIELDS (Extended):
├── LoyaltyTier__c (Picklist: Bronze, Silver, Gold, Platinum)
│   └── Mapped from: Loyalty_Program__dlo.loyalty_tier
│
├── LoyaltyPoints__c (Integer)
│   └── Mapped from: Loyalty_Program__dlo.current_points
│
├── LoyaltyMemberSince__c (Date)
│   └── Mapped from: Loyalty_Program__dlo.member_since
│
├── CustomerSegment__c (Picklist)
│   └── Values: VIP, High Value, Regular, At Risk, Dormant
│   └── Calculated via: Calculated Insight
│
├── PreferredLanguage__c (Picklist: English, Spanish, French)
│   └── Mapped from: Contact_SFDC.Preferred_Language__c
│
├── PreferredChannel__c (Picklist: Email, SMS, App, Mail)
│   └── Calculated from engagement patterns
│
├── CustomerSince__c (Date)
│   └── Calculated: MIN(order_date) from SalesOrder
│
├── LastPurchaseDate__c (Date)
│   └── Calculated Insight from SalesOrder
│
├── TotalLifetimeValue__c (Currency)
│   └── Calculated Insight: SUM(SalesOrder.total_amount)
│
├── AverageOrderValue__c (Currency)
│   └── Calculated Insight: AVG(SalesOrder.total_amount)
│
├── PurchaseFrequency__c (Decimal)
│   └── Calculated: Orders per month
│
├── ProductAffinityCategory__c (Text)
│   └── Example: "Beauty, Fashion, Home"
│   └── Based on purchase history
│
├── ChurnRiskScore__c (Integer: 0-100)
│   └── ML-predicted churn probability
│
├── EmailEngagementScore__c (Decimal)
│   └── Calculated from EmailEngagement DMO
│
├── WebEngagementScore__c (Decimal)
│   └── Calculated from WebEngagement DMO
│
├── CustomerSentiment__c (Picklist: Very Positive, Positive, Neutral, Negative)
│   └── Aggregated from ServiceCase sentiment scores
│
├── HasActiveSubscription__c (Boolean)
│   └── Calculated from Subscription DMO
│
└── SubscriptionLifetimeValue__c (Currency)
    └── Calculated from Subscription DMO

Field Mapping Example:
DLO Field                          →  DMO Field
───────────────────────────────────   ──────────────────────────
Contact_SFDC.FirstName            →  Individual.FirstName
Contact_SFDC.LastName             →  Individual.LastName
Contact_SFDC.Email                →  Individual.EmailAddress
Loyalty_Program__dlo.loyalty_tier →  Individual.LoyaltyTier__c
Loyalty_Program__dlo.current_points → Individual.LoyaltyPoints__c
```

### DMO 2: SalesOrder (Standard DMO with Custom Fields)

```
DMO Name: SalesOrder
Type: Standard DMO (Transaction)
Source DLO: Ecommerce_Orders__dlo

STANDARD FIELDS:
├── Id (String)
├── OrderNumber (String)
├── OrderDate (DateTime)
├── TotalAmount (Currency)
├── OrderStatus (String)
├── BillingStreet (String)
├── BillingCity (String)
├── BillingState (String)
├── BillingPostalCode (String)
└── BillingCountry (String)

CUSTOM FIELDS:
├── OrderSource__c (Picklist: Web, Mobile App, In-Store, Call Center)
│   └── Mapped from: Ecommerce_Orders__dlo.order_source
│
├── DiscountCode__c (String)
│   └── Mapped from: Ecommerce_Orders__dlo.discount_code
│
├── DiscountAmount__c (Currency)
│   └── Mapped from: Ecommerce_Orders__dlo.discount_amount
│
├── TaxAmount__c (Currency)
│   └── Mapped from: Ecommerce_Orders__dlo.tax_amount
│
├── ShippingCost__c (Currency)
│   └── Mapped from: Ecommerce_Orders__dlo.shipping_cost
│
├── PaymentMethod__c (String)
│   └── Mapped from: Ecommerce_Orders__dlo.payment_method
│
├── IsFirstPurchase__c (Boolean)
│   └── Calculated: OrderDate = MIN(OrderDate) for Individual
│
├── DaysSinceLastOrder__c (Integer)
│   └── Calculated Insight
│
├── ProductCategoriesPurchased__c (Text)
│   └── Aggregated from OrderLineItem → Product
│
└── NetOrderValue__c (Currency)
    └── Formula: TotalAmount - DiscountAmount - (returns)

RELATIONSHIPS:
├── Individual_Id (FK) → Individual.Id (Many-to-One)
│   └── Relationship Name: Buyer
│
└── Inverse: Individual → SalesOrder[] (One-to-Many)
    └── Relationship Name: Orders
```

### DMO 3: Subscription (Custom DMO)

```
DMO Name: Subscription__dlm
Type: Custom DMO (Profile Extension)
API Name: Subscription__dlm
Source DLO: Subscription_Data__dlo

PURPOSE:
Track subscription-based revenue and customer subscription health

CUSTOM FIELDS (All):
├── SubscriptionId__c (Text, Primary Key)
│   └── Mapped from: Subscription_Data__dlo.subscription_id
│
├── IndividualId__c (Text, Foreign Key)
│   └── Relationship key to Individual
│
├── SubscriptionType__c (Picklist)
│   └── Values: Beauty Box, Fitness Box, Gourmet Box, Premium Mix
│   └── Mapped from: Subscription_Data__dlo.subscription_type
│
├── PlanName__c (Text)
│   └── Mapped from: Subscription_Data__dlo.plan_name
│
├── StartDate__c (Date)
│   └── Mapped from: Subscription_Data__dlo.start_date
│
├── EndDate__c (Date)
│   └── Mapped from: Subscription_Data__dlo.end_date
│
├── NextRenewalDate__c (Date)
│   └── Mapped from: Subscription_Data__dlo.next_renewal_date
│
├── BillingFrequency__c (Picklist: Monthly, Quarterly, Annual)
│   └── Mapped from: Subscription_Data__dlo.billing_frequency
│
├── Status__c (Picklist: Active, Paused, Cancelled, Expired)
│   └── Mapped from: Subscription_Data__dlo.subscription_status
│
├── MonthlyRecurringRevenue__c (Currency)
│   └── Mapped from: Subscription_Data__dlo.monthly_recurring_revenue
│
├── TotalRevenue__c (Currency)
│   └── Mapped from: Subscription_Data__dlo.total_revenue
│
├── CancellationDate__c (Date)
│   └── Mapped from: Subscription_Data__dlo.cancellation_date
│
├── CancellationReason__c (Text)
│   └── Mapped from: Subscription_Data__dlo.cancellation_reason
│
├── AutoRenewal__c (Boolean)
│   └── Mapped from: Subscription_Data__dlo.auto_renewal
│
├── SubscriptionLengthDays__c (Integer)
│   └── Formula: DATEDIFF(StartDate__c, CURRENT_DATE)
│
├── DaysUntilRenewal__c (Integer)
│   └── Formula: DATEDIFF(CURRENT_DATE, NextRenewalDate__c)
│
├── ChurnRisk__c (Picklist: Low, Medium, High)
│   └── Calculated based on engagement and payment issues
│
└── LifetimeSubscriptionValue__c (Currency)
    └── Calculated: Total revenue generated

RELATIONSHIPS:
├── IndividualId__c (FK) → Individual.Id (Many-to-One)
│   └── Relationship Name: Subscriber
│
└── Inverse: Individual → Subscription[] (One-to-Many)
    └── Relationship Name: Subscriptions
```

### DMO 4: LoyaltyMember (Custom DMO)

```
DMO Name: LoyaltyMember__dlm
Type: Custom DMO (Profile Extension)
Source DLO: Loyalty_Program__dlo

PURPOSE:
Track loyalty program participation and point balances

CUSTOM FIELDS:
├── LoyaltyMemberId__c (Text, Primary Key)
│   └── Mapped from: Loyalty_Program__dlo.loyalty_member_id
│
├── IndividualId__c (Text, Foreign Key)
│
├── Tier__c (Picklist: Bronze, Silver, Gold, Platinum)
│   └── Mapped from: Loyalty_Program__dlo.loyalty_tier
│
├── CurrentPoints__c (Integer)
│   └── Mapped from: Loyalty_Program__dlo.current_points
│
├── LifetimePoints__c (Integer)
│   └── Mapped from: Loyalty_Program__dlo.lifetime_points
│
├── PointsExpiringSoon__c (Integer)
│   └── Mapped from: Loyalty_Program__dlo.points_expiring_soon
│
├── TierStartDate__c (Date)
│   └── Mapped from: Loyalty_Program__dlo.tier_start_date
│
├── TierExpirationDate__c (Date)
│   └── Mapped from: Loyalty_Program__dlo.tier_expiration_date
│
├── MemberSince__c (Date)
│   └── Mapped from: Loyalty_Program__dlo.member_since
│
├── TierUpgradeThreshold__c (Integer)
│   └── Points needed for next tier
│
├── PreferredRewardsCategory__c (String)
│   └── Travel, Shopping, Experiences, Cashback
│
├── RewardsRedeemedCount__c (Integer)
│
├── RewardsRedeemedValue__c (Currency)
│   └── Calculated from LoyaltyRedemption DMO
│
├── LastActivityDate__c (Date)
│
├── TierLikelihood__c (Picklist: At Risk, Stable, Likely to Upgrade)
│   └── Calculated based on point velocity
│
└── EngagementScore__c (Integer: 0-100)
    └── Based on point earning and redemption activity

RELATIONSHIPS:
├── IndividualId__c (FK) → Individual.Id (One-to-One)
│   └── Each person can have one loyalty membership
│
└── LoyaltyRedemption[] ← One-to-Many
    └── Track reward redemptions
```

### DMO 5: ProductReview (Custom DMO)

```
DMO Name: ProductReview__dlm
Type: Custom DMO (Engagement)
Source DLO: Product_Reviews__dlo

PURPOSE:
Capture customer product reviews and ratings

CUSTOM FIELDS:
├── ReviewId__c (Text, Primary Key)
├── IndividualId__c (Text, FK)
├── ProductId__c (Text, FK)
├── Rating__c (Integer: 1-5)
├── ReviewTitle__c (String)
├── ReviewText__c (Text)
├── ReviewDate__c (DateTime)
├── IsVerifiedPurchase__c (Boolean)
├── HelpfulVotes__c (Integer)
├── SentimentScore__c (Decimal: -1.0 to 1.0)
│   └── AI-calculated sentiment
├── ReviewSource__c (Picklist: Website, Email, Mobile App)
└── ModeratedStatus__c (Picklist: Pending, Approved, Rejected)

RELATIONSHIPS:
├── IndividualId__c → Individual.Id (Many-to-One)
│   └── Individual → ProductReview[] (One-to-Many)
│
└── ProductId__c → Product.Id (Many-to-One)
    └── Product → ProductReview[] (One-to-Many)
```

### DMO 6: Product (Standard DMO with Custom Fields)

```
DMO Name: Product
Type: Standard DMO
Source DLO: Product_Catalog__dlo

STANDARD FIELDS:
├── Id (String)
├── ProductName (String)
├── ProductCode (String) - SKU
├── Price (Currency)
├── Description (Text)
└── Category (String)

CUSTOM FIELDS:
├── Brand__c (String)
│   └── Mapped from: Product_Catalog__dlo.brand
│
├── Subcategory__c (String)
│
├── SalePrice__c (Currency)
│
├── Cost__c (Currency)
│   └── For margin calculations
│
├── InventoryQuantity__c (Integer)
│
├── IsSubscriptionEligible__c (Boolean)
│
├── SustainabilityScore__c (Integer: 0-100)
│   └── Environmental impact rating
│
├── AverageRating__c (Decimal)
│   └── Calculated from ProductReview DMO
│
├── ReviewCount__c (Integer)
│   └── Calculated from ProductReview DMO
│
├── LaunchDate__c (Date)
│
├── ProductAttributes__c (JSON/Text)
│   └── Color, Size, Material, etc.
│
├── TotalRevenue__c (Currency)
│   └── Calculated from OrderLineItem
│
├── UnitsSold__c (Integer)
│   └── Calculated from OrderLineItem
│
└── TopAffinityProducts__c (Text)
    └── Products frequently bought together
```

### DMO 7: ServiceCase (Standard DMO with Custom Fields)

```
DMO Name: ServiceCase
Type: Standard DMO (Case)
Source DLO: Service_Cases__dlo

STANDARD FIELDS:
├── Id (String)
├── CaseNumber (String)
├── Subject (String)
├── Description (Text)
├── Status (String)
├── Priority (String)
├── CreatedDate (DateTime)
└── ClosedDate (DateTime)

CUSTOM FIELDS:
├── IndividualId__c (Text, FK)
├── CaseType__c (Picklist: Inquiry, Complaint, Return, Technical)
├── Channel__c (Picklist: Phone, Email, Chat, Social)
├── ResolutionTimeHours__c (Decimal)
├── FirstResponseTimeHours__c (Decimal)
├── SentimentScore__c (Decimal: -1.0 to 1.0)
├── CSATScore__c (Integer: 1-5)
├── RelatedOrderId__c (Text, FK)
│   └── Link to SalesOrder if order-related
└── AgentId__c (Text)

RELATIONSHIPS:
├── IndividualId__c → Individual.Id (Many-to-One)
└── RelatedOrderId__c → SalesOrder.Id (Many-to-One)
```

### DMO 8: PaymentMethod (Custom DMO)

```
DMO Name: PaymentMethod__dlm
Type: Custom DMO
Purpose: Track customer payment preferences

CUSTOM FIELDS:
├── PaymentMethodId__c (Text, Primary Key)
├── IndividualId__c (Text, FK)
├── PaymentType__c (Picklist: Credit Card, Debit Card, PayPal, Apple Pay, Google Pay)
├── CardBrand__c (String: Visa, MasterCard, Amex)
├── LastFourDigits__c (String)
├── ExpirationDate__c (Date)
├── IsDefault__c (Boolean)
├── BillingZipCode__c (String)
├── AddedDate__c (DateTime)
├── LastUsedDate__c (DateTime)
└── Status__c (Picklist: Active, Expired, Removed)

RELATIONSHIPS:
└── IndividualId__c → Individual.Id (Many-to-One)
    └── Individual → PaymentMethod[] (One-to-Many)
```

---

## Relationship Mapping: Building the Knowledge Graph {#relationships}

### Complete Relationship Schema

```
                            ┌──────────────────┐
                            │   Individual     │ ← Central Node
                            │   (Person)       │
                            └────────┬─────────┘
                                     │
                  ┌──────────────────┼──────────────────┐
                  │                  │                  │
         ┌────────▼────────┐ ┌──────▼─────┐  ┌────────▼────────┐
         │   SalesOrder    │ │Subscription│  │  LoyaltyMember  │
         │   (purchased)   │ │(subscribed)│  │   (member of)   │
         └────────┬────────┘ └──────┬─────┘  └────────┬────────┘
                  │                  │                  │
         ┌────────▼────────┐        │         ┌────────▼────────┐
         │ OrderLineItem   │        │         │LoyaltyRedemption│
         │  (contains)     │        │         │  (redeemed)     │
         └────────┬────────┘        │         └─────────────────┘
                  │                  │
         ┌────────▼────────┐        │
         │    Product      │◄───────┘
         │   (bought)      │
         └────────┬────────┘
                  │
         ┌────────▼────────┐
         │ ProductReview   │
         │  (reviewed by)  │
         └─────────────────┘

Additional Relationships:
Individual → EmailEngagement (received emails)
Individual → WebEngagement (website activity)
Individual → ServiceCase (support tickets)
Individual → PaymentMethod (payment options)
Product → ProductCategory (belongs to)
Product → Brand (manufactured by)
```

### Relationship Definitions

#### 1. Individual → SalesOrder (One-to-Many)

```
Relationship Configuration:
├── Name: Orders
├── Type: One-to-Many
├── From: Individual
├── To: SalesOrder
├── Key: SalesOrder.IndividualId__c → Individual.Id
└── Cardinality: One Individual → Many SalesOrders

Use Cases:
├── "Show all orders for this customer"
├── "Calculate total lifetime value"
├── "Identify high-value customers (sum of orders)"
└── "Find customers with no orders in 90 days"

Query Example:
SELECT
    Individual.Id,
    Individual.FirstName,
    Individual.LastName,
    COUNT(Orders.OrderNumber) AS TotalOrders,
    SUM(Orders.TotalAmount) AS LifetimeValue
FROM Individual
LEFT JOIN SalesOrder AS Orders ON Orders.IndividualId = Individual.Id
GROUP BY Individual.Id
```

#### 2. Individual → Subscription (One-to-Many)

```
Relationship Configuration:
├── Name: Subscriptions
├── Type: One-to-Many
├── From: Individual
├── To: Subscription__dlm
├── Key: Subscription__dlm.IndividualId__c → Individual.Id
└── Cardinality: One Individual → Many Subscriptions

Use Cases:
├── "Show all active subscriptions"
├── "Calculate subscription lifetime value"
├── "Predict subscription churn"
└── "Upsell opportunities (add more subscriptions)"

Affinity Example:
"Customers with Beauty Box subscriptions have 65% affinity
 for purchasing premium skincare products"
```

#### 3. Individual → LoyaltyMember (One-to-One)

```
Relationship Configuration:
├── Name: LoyaltyProfile
├── Type: One-to-One
├── From: Individual
├── To: LoyaltyMember__dlm
├── Key: LoyaltyMember__dlm.IndividualId__c → Individual.Id
└── Cardinality: One Individual → One LoyaltyMember

Use Cases:
├── "Display loyalty tier in profile"
├── "Show points balance"
├── "Recommend tier upgrade path"
└── "Personalize rewards based on tier"
```

#### 4. SalesOrder → OrderLineItem (One-to-Many)

```
Relationship Configuration:
├── Name: LineItems
├── Type: One-to-Many
├── From: SalesOrder
├── To: OrderLineItem__dlm
├── Key: OrderLineItem__dlm.OrderId__c → SalesOrder.Id
└── Cardinality: One Order → Many Line Items

Use Cases:
├── "Show order details"
├── "Calculate product-level revenue"
├── "Identify product affinities (cart analysis)"
└── "Analyze average items per order"
```

#### 5. OrderLineItem → Product (Many-to-One)

```
Relationship Configuration:
├── Name: Product
├── Type: Many-to-One
├── From: OrderLineItem__dlm
├── To: Product
├── Key: OrderLineItem__dlm.ProductId__c → Product.Id
└── Cardinality: Many Line Items → One Product

Use Cases:
├── "Product performance analysis"
├── "Bestseller identification"
├── "Inventory optimization"
└── "Cross-sell recommendations"

Knowledge Graph Query:
"Find all customers who bought Product X"
→ Product → OrderLineItem → SalesOrder → Individual
```

#### 6. Individual → ProductReview (One-to-Many)

```
Relationship Configuration:
├── Name: Reviews
├── Type: One-to-Many
├── From: Individual
├── To: ProductReview__dlm
├── Key: ProductReview__dlm.IndividualId__c → Individual.Id
└── Cardinality: One Individual → Many Reviews

Use Cases:
├── "Identify brand advocates (high ratings)"
├── "Calculate review sentiment by customer segment"
├── "Reward customers for reviews"
└── "Identify detractors (low ratings) for outreach"
```

#### 7. Product → ProductReview (One-to-Many)

```
Relationship Configuration:
├── Name: Reviews
├── Type: One-to-Many
├── From: Product
├── To: ProductReview__dlm
├── Key: ProductReview__dlm.ProductId__c → Product.Id
└── Cardinality: One Product → Many Reviews

Use Cases:
├── "Display product ratings"
├── "Identify poorly-rated products"
├── "Sentiment analysis by product"
└── "Social proof for marketing"
```

#### 8. Individual → ServiceCase (One-to-Many)

```
Relationship Configuration:
├── Name: ServiceCases
├── Type: One-to-Many
├── From: Individual
├── To: ServiceCase
├── Key: ServiceCase.IndividualId__c → Individual.Id
└── Cardinality: One Individual → Many Cases

Use Cases:
├── "Customer health scoring (complaint frequency)"
├── "VIP flagging (high-value customer issues)"
├── "Proactive outreach (negative sentiment)"
└── "Service quality metrics by customer segment"
```

### Multi-Hop Relationship Queries

#### Example 1: Product Affinity (2-hop)
```
Query: "What products do customers who bought Product A also buy?"

Path:
Product A → OrderLineItem → SalesOrder → Individual
           ↓
Individual → SalesOrder → OrderLineItem → Product B

SQL Logic:
SELECT
    p2.ProductName,
    COUNT(DISTINCT i.Id) AS CustomerCount,
    (COUNT(DISTINCT i.Id) / TotalCustomers) AS AffinityScore
FROM Product p1
JOIN OrderLineItem li1 ON li1.ProductId = p1.Id
JOIN SalesOrder so1 ON so1.Id = li1.OrderId
JOIN Individual i ON i.Id = so1.IndividualId
JOIN SalesOrder so2 ON so2.IndividualId = i.Id
JOIN OrderLineItem li2 ON li2.OrderId = so2.Id
JOIN Product p2 ON p2.Id = li2.ProductId
WHERE p1.ProductName = 'Premium Matte Lipstick'
  AND p2.Id != p1.Id
GROUP BY p2.ProductName
ORDER BY AffinityScore DESC
LIMIT 10

Result:
Product B (Makeup Remover) - 72% affinity
Product C (Foundation) - 68% affinity
Product D (Mascara) - 55% affinity
```

#### Example 2: Channel Preference (Multi-entity)
```
Query: "What channels do high-value customers prefer?"

Path:
Individual → SalesOrder (filter: LifetimeValue > $1000)
Individual → EmailEngagement
Individual → WebEngagement
Individual → ServiceCase

SQL Logic:
WITH HighValueCustomers AS (
    SELECT
        i.Id,
        SUM(so.TotalAmount) AS LTV
    FROM Individual i
    JOIN SalesOrder so ON so.IndividualId = i.Id
    GROUP BY i.Id
    HAVING SUM(so.TotalAmount) > 1000
)
SELECT
    'Email' AS Channel,
    COUNT(DISTINCT ee.IndividualId) AS EngagedCustomers
FROM EmailEngagement ee
JOIN HighValueCustomers hvc ON hvc.Id = ee.IndividualId
WHERE ee.EventType = 'click'

UNION ALL

SELECT
    'Web' AS Channel,
    COUNT(DISTINCT we.IndividualId)
FROM WebEngagement we
JOIN HighValueCustomers hvc ON hvc.Id = we.IndividualId
```

---

## Affinities and Calculated Insights {#affinities}

### What are Affinities?

**Affinities** are calculated relationships showing how entities are connected beyond direct relationships.

```
Types of Affinities:
├── Product Affinity (products bought together)
├── Category Affinity (customer preference for categories)
├── Brand Affinity (customer loyalty to brands)
├── Channel Affinity (preferred communication channel)
├── Time-based Affinity (purchase patterns by time)
└── Behavioral Affinity (similar customer behaviors)
```

### Calculated Insights on Individual

#### Insight 1: Product Category Affinity

```
Name: ProductCategoryAffinity__ci
Type: Calculated Insight
Output: Text (comma-separated categories)
Schedule: Daily at 3 AM

Logic:
SELECT
    Individual.Id,
    GROUP_CONCAT(
        CategoryRank.Category
        ORDER BY CategoryRank.PurchaseCount DESC
    ) AS ProductCategoryAffinity
FROM Individual
JOIN (
    SELECT
        so.IndividualId,
        p.Category,
        COUNT(*) AS PurchaseCount,
        SUM(li.TotalAmount) AS CategorySpend
    FROM SalesOrder so
    JOIN OrderLineItem li ON li.OrderId = so.Id
    JOIN Product p ON p.Id = li.ProductId
    WHERE so.OrderDate >= DATEADD(year, -1, CURRENT_DATE)
    GROUP BY so.IndividualId, p.Category
) CategoryRank ON CategoryRank.IndividualId = Individual.Id
GROUP BY Individual.Id

Result on Unified Individual:
ProductCategoryAffinity__ci: "Beauty, Fashion, Home Decor"
```

#### Insight 2: Brand Affinity Score

```
Name: TopBrandAffinity__ci
Type: Calculated Insight
Output: Text
Schedule: Weekly

Logic:
WITH BrandPurchases AS (
    SELECT
        so.IndividualId,
        p.Brand__c,
        COUNT(DISTINCT so.Id) AS OrderCount,
        SUM(li.Quantity) AS UnitsBought,
        SUM(li.TotalAmount) AS BrandSpend
    FROM SalesOrder so
    JOIN OrderLineItem li ON li.OrderId = so.Id
    JOIN Product p ON p.Id = li.ProductId
    GROUP BY so.IndividualId, p.Brand__c
),
RankedBrands AS (
    SELECT
        IndividualId,
        Brand__c,
        ROW_NUMBER() OVER (
            PARTITION BY IndividualId
            ORDER BY BrandSpend DESC
        ) AS BrandRank
    FROM BrandPurchases
)
SELECT
    IndividualId,
    Brand__c AS TopBrandAffinity
FROM RankedBrands
WHERE BrandRank = 1

Result:
TopBrandAffinity__ci: "LuxeBeauty"
```

#### Insight 3: Subscription-to-Purchase Affinity

```
Name: SubscriptionPurchaseAffinity__ci
Type: Calculated Insight
Output: Decimal (affinity score 0-100)
Schedule: Daily

Purpose:
Measure how subscription ownership influences e-commerce purchases

Logic:
SELECT
    i.Id,
    CASE
        WHEN sub.Status__c = 'active' THEN
            (COUNT(DISTINCT so.Id) * 1.0 / NULLIF(
                DATEDIFF(day, sub.StartDate__c, CURRENT_DATE) / 30,
                0
            )) * 10
        ELSE 0
    END AS SubscriptionPurchaseAffinity
FROM Individual i
LEFT JOIN Subscription__dlm sub ON sub.IndividualId__c = i.Id
LEFT JOIN SalesOrder so ON so.IndividualId = i.Id
    AND so.OrderDate >= sub.StartDate__c
GROUP BY i.Id, sub.Status__c, sub.StartDate__c

Insight:
"Customers with active Beauty Box subscriptions make 3.5x more
 e-commerce purchases per month than non-subscribers"
```

#### Insight 4: Channel Preference Score

```
Name: PreferredChannel__ci
Type: Calculated Insight
Output: Text (Email, Web, Mobile App, In-Store)
Schedule: Weekly

Logic:
WITH ChannelEngagement AS (
    SELECT
        IndividualId,
        'Email' AS Channel,
        COUNT(*) AS EngagementCount
    FROM EmailEngagement
    WHERE EventType IN ('open', 'click')
      AND EventDate >= DATEADD(day, -90, CURRENT_DATE)
    GROUP BY IndividualId

    UNION ALL

    SELECT
        IndividualId,
        'Web' AS Channel,
        COUNT(*)
    FROM WebEngagement
    WHERE EventType = 'page_view'
      AND EventDate >= DATEADD(day, -90, CURRENT_DATE)
    GROUP BY IndividualId

    UNION ALL

    SELECT
        IndividualId,
        'In-Store' AS Channel,
        COUNT(*)
    FROM SalesOrder
    WHERE OrderSource__c = 'In-Store'
      AND OrderDate >= DATEADD(day, -90, CURRENT_DATE)
    GROUP BY IndividualId
),
RankedChannels AS (
    SELECT
        IndividualId,
        Channel,
        ROW_NUMBER() OVER (
            PARTITION BY IndividualId
            ORDER BY EngagementCount DESC
        ) AS ChannelRank
    FROM ChannelEngagement
)
SELECT
    IndividualId,
    Channel AS PreferredChannel
FROM RankedChannels
WHERE ChannelRank = 1

Result:
PreferredChannel__ci: "Email"
```

#### Insight 5: Customer Lifetime Value (with Subscription)

```
Name: TotalLifetimeValue__ci
Type: Calculated Insight
Output: Currency
Schedule: Daily

Logic:
SELECT
    i.Id,
    COALESCE(SUM(so.TotalAmount), 0) +
    COALESCE(SUM(sub.TotalRevenue__c), 0) AS TotalLifetimeValue
FROM Individual i
LEFT JOIN SalesOrder so ON so.IndividualId = i.Id
LEFT JOIN Subscription__dlm sub ON sub.IndividualId__c = i.Id
GROUP BY i.Id

Result:
TotalLifetimeValue__ci: $8,450.75
  ├── E-commerce: $5,200.50
  └── Subscriptions: $3,250.25
```

#### Insight 6: Churn Risk Score

```
Name: ChurnRiskScore__ci
Type: Calculated Insight
Output: Integer (0-100)
Schedule: Daily

Logic:
WITH CustomerMetrics AS (
    SELECT
        i.Id,
        DATEDIFF(day, MAX(so.OrderDate), CURRENT_DATE) AS DaysSinceLastOrder,
        COUNT(DISTINCT so.Id) AS TotalOrders,
        AVG(ee.OpenRate) AS AvgEmailOpenRate,
        COUNT(DISTINCT sc.Id) AS TotalSupportCases,
        AVG(sc.SentimentScore__c) AS AvgSentiment
    FROM Individual i
    LEFT JOIN SalesOrder so ON so.IndividualId = i.Id
    LEFT JOIN EmailEngagement ee ON ee.IndividualId = i.Id
    LEFT JOIN ServiceCase sc ON sc.IndividualId__c = i.Id
    GROUP BY i.Id
)
SELECT
    Id,
    LEAST(100, GREATEST(0,
        (DaysSinceLastOrder / 3.0) +  -- Recency penalty
        (CASE WHEN TotalOrders < 2 THEN 30 ELSE 0 END) +  -- Low engagement
        (CASE WHEN AvgEmailOpenRate < 0.1 THEN 20 ELSE 0 END) +  -- Email disengagement
        (CASE WHEN AvgSentiment < 0 THEN 25 ELSE 0 END)  -- Negative sentiment
    )) AS ChurnRiskScore
FROM CustomerMetrics

Segments based on score:
├── 0-25: Low Risk (Green)
├── 26-50: Medium Risk (Yellow)
├── 51-75: High Risk (Orange)
└── 76-100: Critical Risk (Red)
```

#### Insight 7: Product Recommendation Score

```
Name: TopRecommendedProducts__ci
Type: Calculated Insight
Output: Text (comma-separated product IDs)
Schedule: Daily

Logic:
-- Collaborative filtering based on similar customers
WITH SimilarCustomers AS (
    SELECT
        i1.Id AS IndividualId,
        i2.Id AS SimilarIndividualId,
        -- Similarity based on shared purchases
        COUNT(DISTINCT CASE
            WHEN p1.Category = p2.Category THEN p1.Category
        END) AS SharedCategories
    FROM Individual i1
    JOIN SalesOrder so1 ON so1.IndividualId = i1.Id
    JOIN OrderLineItem li1 ON li1.OrderId = so1.Id
    JOIN Product p1 ON p1.Id = li1.ProductId
    JOIN OrderLineItem li2 ON li2.ProductId = p1.Id
    JOIN SalesOrder so2 ON so2.Id = li2.OrderId
    JOIN Individual i2 ON i2.Id = so2.IndividualId
    WHERE i1.Id != i2.Id
    GROUP BY i1.Id, i2.Id
    HAVING COUNT(DISTINCT p1.Id) >= 3  -- At least 3 shared products
),
RecommendedProducts AS (
    SELECT
        sc.IndividualId,
        p.Id AS ProductId,
        COUNT(*) AS PurchasesBySimilar,
        AVG(pr.Rating__c) AS AvgRating
    FROM SimilarCustomers sc
    JOIN SalesOrder so ON so.IndividualId = sc.SimilarIndividualId
    JOIN OrderLineItem li ON li.OrderId = so.Id
    JOIN Product p ON p.Id = li.ProductId
    LEFT JOIN ProductReview pr ON pr.ProductId__c = p.Id
    WHERE NOT EXISTS (
        -- Exclude products already purchased
        SELECT 1
        FROM SalesOrder so2
        JOIN OrderLineItem li2 ON li2.OrderId = so2.Id
        WHERE so2.IndividualId = sc.IndividualId
          AND li2.ProductId = p.Id
    )
    GROUP BY sc.IndividualId, p.Id
    HAVING AVG(pr.Rating__c) >= 4.0  -- High-rated products only
),
TopRecs AS (
    SELECT
        IndividualId,
        ProductId,
        ROW_NUMBER() OVER (
            PARTITION BY IndividualId
            ORDER BY PurchasesBySimilar DESC, AvgRating DESC
        ) AS RecRank
    FROM RecommendedProducts
)
SELECT
    IndividualId,
    GROUP_CONCAT(ProductId ORDER BY RecRank) AS TopRecommendedProducts
FROM TopRecs
WHERE RecRank <= 5  -- Top 5 recommendations
GROUP BY IndividualId

Result:
TopRecommendedProducts__ci: "PROD-123,PROD-456,PROD-789"
```

### Affinity Matrix Example

```
Product Affinity Matrix (Top 5 Products):

              │ Lipstick │ Foundation │ Mascara │ Serum │ Cleanser
──────────────┼──────────┼────────────┼─────────┼───────┼──────────
Lipstick      │   100%   │    68%     │   55%   │  42%  │   38%
Foundation    │    68%   │   100%     │   72%   │  65%  │   58%
Mascara       │    55%   │    72%     │  100%   │  48%  │   45%
Serum         │    42%   │    65%     │   48%   │ 100%  │   82%
Cleanser      │    38%   │    58%     │   45%   │  82%  │  100%

Interpretation:
- Customers who buy Foundation have 72% probability of buying Mascara
- Customers who buy Serum have 82% probability of buying Cleanser
→ Bundle recommendations: "Serum + Cleanser" combo offer
```

---

## Knowledge Graph Visualization {#kg-visualization}

### Complete Knowledge Graph Structure

```
                    ┌───────────────────────────┐
                    │   Sarah Johnson           │
                    │   Individual ID: IND-789  │
                    │   ──────────────────────  │
                    │   Email: sarah@email.com  │
                    │   Loyalty: Gold (12.5K pts)│
                    │   LTV: $8,450             │
                    │   Churn Risk: Low (15)    │
                    └─────────────┬─────────────┘
                                  │
        ┌─────────────────────────┼─────────────────────────┐
        │                         │                         │
        │                         │                         │
┌───────▼────────┐      ┌─────────▼────────┐     ┌─────────▼────────┐
│  Subscriptions │      │   Purchase History│     │ Loyalty Profile  │
├────────────────┤      ├──────────────────┤     ├──────────────────┤
│ Beauty Box     │      │ 24 Orders        │     │ Gold Member      │
│ Active         │      │ Last: 2025-01-05 │     │ Since: 2023-03   │
│ $49.99/mo      │      │ AOV: $245        │     │ 12,500 points    │
│ Since: 2024-06 │      │                  │     │ 8 Rewards Used   │
└────────────────┘      └──────────────────┘     └──────────────────┘
                                  │
                ┌─────────────────┼─────────────────┐
                │                 │                 │
        ┌───────▼────────┐ ┌─────▼──────┐  ┌──────▼────────┐
        │ Recent Orders  │ │  Products  │  │ Engagement    │
        ├────────────────┤ ├────────────┤  ├───────────────┤
        │ ORD-2025-00123 │ │ Lipstick   │  │ Email: High   │
        │ Jan 9, 2025    │ │ Rating: 5★ │  │ Opens: 85%    │
        │ $249.99        │ │ Purchased  │  │ Clicks: 45%   │
        │ ──────────────│ │ 8 times    │  │               │
        │ ORD-2025-00087 │ │            │  │ Web: Medium   │
        │ Jan 5, 2025    │ │ Foundation │  │ Visits: 12/mo │
        │ $189.50        │ │ Rating: 5★ │  │               │
        └────────────────┘ │ Purchased  │  │ Mobile: High  │
                           │ 6 times    │  │ Sessions: 25  │
                           └────────────┘  └───────────────┘
                                  │
                        ┌─────────▼──────────┐
                        │  Product Reviews   │
                        ├────────────────────┤
                        │ 12 Reviews Written │
                        │ Avg Rating: 4.8★   │
                        │ Sentiment: +0.85   │
                        └────────────────────┘

Affinities:
├── Category: Beauty (85%), Fashion (10%), Home (5%)
├── Brand: LuxeBeauty (70%), PremiumStyle (20%)
├── Channel: Email (45%), Mobile App (35%), Web (20%)
└── Product Recs: Serum, Primer, Setting Spray
```

### Graph Query Examples

#### Query 1: Find Influencers (High Review + High Social Sharing)

```
MATCH (individual:Individual)-[:WROTE]->(review:ProductReview)
WHERE review.Rating >= 4
WITH individual, COUNT(review) AS ReviewCount
WHERE ReviewCount >= 10
MATCH (individual)-[:SHARED]->(social:SocialPost)
WITH individual, ReviewCount, COUNT(social) AS ShareCount
WHERE ShareCount >= 5
RETURN individual.Name, ReviewCount, ShareCount, individual.LoyaltyTier
ORDER BY ReviewCount + ShareCount DESC
LIMIT 100

Use Case:
Identify brand advocates for:
├── Ambassador programs
├── User-generated content campaigns
├── Product launches (early access)
└── Referral program incentives
```

#### Query 2: Subscription Expansion Opportunity

```
MATCH (individual:Individual)-[:HAS_SUBSCRIPTION]->(sub:Subscription)
WHERE sub.Status = 'active'
  AND sub.SubscriptionType = 'Beauty Box'
MATCH (individual)-[:PURCHASED]->(order:SalesOrder)-[:CONTAINS]->(item:OrderLineItem)-[:IS_PRODUCT]->(product:Product)
WHERE product.Category = 'Fitness'
  AND product.IsSubscriptionEligible = true
WITH individual, COUNT(DISTINCT product) AS FitnessProductsPurchased
WHERE FitnessProductsPurchased >= 3
RETURN individual.Id, individual.Email, FitnessProductsPurchased

Use Case:
Target customers for Fitness Box upsell:
"You love fitness products! Try our Fitness Box for 20% off"
```

#### Query 3: Churn Prevention - At-Risk High-Value Customers

```
MATCH (individual:Individual)
WHERE individual.ChurnRiskScore >= 70
  AND individual.TotalLifetimeValue >= 1000
OPTIONAL MATCH (individual)-[:HAS_CASE]->(case:ServiceCase)
WHERE case.CreatedDate >= DATE_SUB(CURRENT_DATE, INTERVAL 30 DAY)
  AND case.SentimentScore < 0
RETURN
    individual.Id,
    individual.Name,
    individual.Email,
    individual.ChurnRiskScore,
    individual.TotalLifetimeValue,
    COUNT(case) AS RecentNegativeCases,
    individual.LastPurchaseDate

Use Case:
Proactive outreach:
├── Personalized discount (15-20% off)
├── Account manager assignment
├── Service recovery (resolve complaints)
└── Win-back campaign
```

#### Query 4: Product Bundle Recommendations

```
-- Find products frequently bought together
MATCH (p1:Product)<-[:IS_PRODUCT]-(item1:OrderLineItem)<-[:CONTAINS]-(order:SalesOrder)-[:CONTAINS]->(item2:OrderLineItem)-[:IS_PRODUCT]->(p2:Product)
WHERE p1.Id < p2.Id  -- Avoid duplicates
WITH p1, p2, COUNT(DISTINCT order) AS CoOccurrence
WITH p1, p2, CoOccurrence,
     (CoOccurrence * 1.0 / (
        SELECT COUNT(DISTINCT order2)
        FROM SalesOrder order2
        JOIN OrderLineItem item3 ON order2.Id = item3.OrderId
        WHERE item3.ProductId = p1.Id
     )) AS Affinity
WHERE Affinity >= 0.5  -- 50%+ affinity
RETURN
    p1.ProductName AS Product1,
    p2.ProductName AS Product2,
    Affinity,
    CoOccurrence AS TimesBoughtTogether
ORDER BY Affinity DESC
LIMIT 20

Result:
Product1: "Hydrating Serum"
Product2: "Daily Moisturizer"
Affinity: 82%
TimesBoughtTogether: 1,247

Action:
Create bundle: "Complete Hydration Kit" (Serum + Moisturizer) at 15% discount
```

---

## Advanced Use Cases {#use-cases}

### Use Case 1: Next Best Action (NBA)

**Goal:** Determine the optimal action for each customer in real-time

```
NBA Decision Engine (Calculated Insight):

Input Signals:
├── Customer Attributes (Tier, LTV, Churn Risk)
├── Recent Behavior (Purchases, Engagement)
├── Product Affinities
├── Subscription Status
├── Service History
└── Seasonal Trends

Decision Logic:
IF ChurnRiskScore > 70 AND TotalLifetimeValue > 1000 THEN
    Action = "Retention Offer - VIP Discount"
    Priority = "Critical"

ELSE IF HasActiveSubscription = false AND SubscriptionAffinityScore > 60 THEN
    Action = "Subscription Trial Offer"
    Priority = "High"

ELSE IF DaysSinceLastPurchase BETWEEN 30 AND 60
    AND EmailEngagementScore > 50 THEN
    Action = "Product Recommendation Email"
    Priority = "Medium"

ELSE IF LoyaltyPoints >= TierUpgradeThreshold * 0.9 THEN
    Action = "Tier Upgrade Incentive"
    Priority = "Medium"

ELSE IF CartAbandoned = true AND CartValue > 100 THEN
    Action = "Cart Recovery - 10% Discount"
    Priority = "High"

ELSE
    Action = "Engagement Nurture"
    Priority = "Low"

Output (on Unified Individual):
├── NextBestAction__c: "Subscription Trial Offer"
├── NBA_Priority__c: "High"
├── NBA_Channel__c: "Email" (based on PreferredChannel__ci)
└── NBA_ExpirationDate__c: DATE_ADD(CURRENT_DATE, 7)  -- Expires in 7 days
```

### Use Case 2: Personalized Homepage

**Goal:** Customize website experience based on knowledge graph

```
Homepage Personalization API Response:

GET /api/v1/personalization/homepage?individual_id=IND-789

Response:
{
  "individualId": "IND-789",
  "name": "Sarah",
  "loyaltyTier": "Gold",
  "loyaltyPoints": 12500,
  "personalizedGreeting": "Welcome back, Sarah! You're only 2,500 points from Platinum status!",

  "heroSection": {
    "contentType": "subscription_upsell",
    "headline": "Complete Your Beauty Routine",
    "subheadline": "Try our Skincare Essentials Box",
    "cta": "Start Free Trial",
    "reason": "Based on your love for premium skincare"
  },

  "productRecommendations": [
    {
      "productId": "PROD-789",
      "productName": "Age-Defying Serum",
      "reason": "Customers like you also bought this",
      "affinityScore": 0.85
    },
    {
      "productId": "PROD-456",
      "productName": "Hydrating Primer",
      "reason": "Perfect for your makeup collection",
      "affinityScore": 0.78
    }
  ],

  "loyaltyWidget": {
    "currentTier": "Gold",
    "pointsBalance": 12500,
    "pointsToNextTier": 2500,
    "nextTier": "Platinum",
    "expiringPoints": 500,
    "expiringDate": "2025-02-01"
  },

  "subscriptionWidget": {
    "status": "active",
    "nextBoxDate": "2025-01-15",
    "boxType": "Beauty Box - Premium",
    "canUpgrade": true,
    "upgradeOffer": "Add Fitness Box for $39.99/mo (20% off)"
  },

  "recentlyViewed": [
    "PROD-123", "PROD-456", "PROD-789"
  ],

  "exclusiveOffers": [
    {
      "offerId": "OFFER-001",
      "title": "Gold Member Exclusive: 15% Off",
      "expiresIn": "2 days",
      "reason": "Thank you for being a loyal Gold member"
    }
  ]
}

Knowledge Graph Queries Used:
├── Individual.LoyaltyTier__c
├── Individual.TopRecommendedProducts__ci
├── Individual.ProductCategoryAffinity__ci
├── Subscription.NextRenewalDate__c
├── Individual.LastPurchaseDate__c
└── Recent WebEngagement (page views)
```

### Use Case 3: Predictive Inventory Management

**Goal:** Forecast demand based on subscription and purchase patterns

```
Inventory Forecast (Product Level):

Query:
WITH SubscriptionDemand AS (
    SELECT
        p.ProductId,
        COUNT(DISTINCT sub.IndividualId__c) AS ActiveSubscribers,
        p.SubscriptionBoxQuantity AS UnitsPerBox,
        sub.BillingFrequency__c
    FROM Subscription__dlm sub
    JOIN SubscriptionBoxContent sbc ON sbc.SubscriptionType = sub.SubscriptionType__c
    JOIN Product p ON p.Id = sbc.ProductId
    WHERE sub.Status__c = 'active'
    GROUP BY p.ProductId, p.SubscriptionBoxQuantity, sub.BillingFrequency__c
),
PurchaseDemand AS (
    SELECT
        li.ProductId,
        AVG(MonthlyUnits.Units) AS AvgMonthlyUnits,
        STDDEV(MonthlyUnits.Units) AS StdDevUnits
    FROM (
        SELECT
            li2.ProductId,
            DATE_TRUNC('month', so.OrderDate) AS Month,
            SUM(li2.Quantity) AS Units
        FROM SalesOrder so
        JOIN OrderLineItem li2 ON li2.OrderId = so.Id
        WHERE so.OrderDate >= DATEADD(month, -12, CURRENT_DATE)
        GROUP BY li2.ProductId, DATE_TRUNC('month', so.OrderDate)
    ) MonthlyUnits
    GROUP BY ProductId
)
SELECT
    p.ProductId,
    p.ProductName,
    p.InventoryQuantity AS CurrentStock,
    COALESCE(sd.ActiveSubscribers * sd.UnitsPerBox, 0) AS MonthlySubscriptionDemand,
    COALESCE(pd.AvgMonthlyUnits, 0) AS MonthlyPurchaseDemand,
    (COALESCE(sd.ActiveSubscribers * sd.UnitsPerBox, 0) +
     COALESCE(pd.AvgMonthlyUnits, 0)) AS TotalMonthlyDemand,
    CASE
        WHEN p.InventoryQuantity < (
            COALESCE(sd.ActiveSubscribers * sd.UnitsPerBox, 0) +
            COALESCE(pd.AvgMonthlyUnits, 0)
        ) THEN 'Reorder Now'
        WHEN p.InventoryQuantity < (
            (COALESCE(sd.ActiveSubscribers * sd.UnitsPerBox, 0) +
             COALESCE(pd.AvgMonthlyUnits, 0)) * 2
        ) THEN 'Reorder Soon'
        ELSE 'Sufficient Stock'
    END AS InventoryStatus
FROM Product p
LEFT JOIN SubscriptionDemand sd ON sd.ProductId = p.ProductId
LEFT JOIN PurchaseDemand pd ON pd.ProductId = p.ProductId

Result:
ProductId: PROD-123
ProductName: "Premium Matte Lipstick"
CurrentStock: 450 units
MonthlySubscriptionDemand: 800 units (200 subscribers × 4 per box)
MonthlyPurchaseDemand: 320 units
TotalMonthlyDemand: 1,120 units
InventoryStatus: "Reorder Now"

Action:
Purchase Order: 1,500 units (1.3x monthly demand for safety stock)
```

### Use Case 4: Loyalty Tier Optimization

**Goal:** Maximize loyalty engagement through data-driven tier benefits

```
Tier Behavior Analysis:

Query:
SELECT
    lm.Tier__c,
    COUNT(DISTINCT lm.IndividualId__c) AS Members,
    AVG(i.TotalLifetimeValue__c) AS AvgLTV,
    AVG(i.EmailEngagementScore__c) AS AvgEmailEngagement,
    AVG(i.PurchaseFrequency__c) AS AvgPurchaseFrequency,
    SUM(CASE WHEN sub.Status__c = 'active' THEN 1 ELSE 0 END) * 1.0 / COUNT(*) AS SubscriptionRate,
    AVG(lm.RewardsRedeemedCount__c) AS AvgRedemptions,
    AVG(i.ChurnRiskScore__c) AS AvgChurnRisk
FROM LoyaltyMember__dlm lm
JOIN Individual i ON i.Id = lm.IndividualId__c
LEFT JOIN Subscription__dlm sub ON sub.IndividualId__c = i.Id
GROUP BY lm.Tier__c

Results:
┌──────────┬─────────┬──────────┬───────────┬──────────┬─────────────┬──────────┐
│  Tier    │ Members │ Avg LTV  │ Email Eng │ Purch/Mo │ Sub Rate    │ Churn    │
├──────────┼─────────┼──────────┼───────────┼──────────┼─────────────┼──────────┤
│ Bronze   │  45,000 │  $425    │   35      │   0.8    │    12%      │   55     │
│ Silver   │  18,000 │  $1,240  │   52      │   1.5    │    35%      │   35     │
│ Gold     │   8,500 │  $3,850  │   68      │   2.8    │    68%      │   18     │
│ Platinum │   2,100 │  $8,900  │   82      │   4.2    │    89%      │    8     │
└──────────┴─────────┴──────────┴───────────┴──────────┴─────────────┴──────────┘

Insights:
├── Platinum members have 11x higher LTV than Bronze
├── Subscription rate increases dramatically with tier
├── Churn risk decreases as tier increases
└── Gold → Platinum jump shows biggest LTV increase

Actions:
1. Incentivize Bronze → Silver upgrades (biggest population)
2. Gold members are prime subscription targets (68% already have one)
3. Platinum tier benefits should focus on retention (very low churn)
4. Create "Fast Track to Silver" program for engaged Bronze members
```

---

## Multi-Industry Examples {#industry-examples}

### Healthcare: Patient Knowledge Graph

```
Entities (DMOs):
├── Individual (Patient) - Standard DMO
│   ├── Custom Fields: PrimaryCarePhysician__c, InsurancePlan__c, ChronicConditions__c
│   └── Relationships:
│       ├── Appointments (One-to-Many)
│       ├── Prescriptions (One-to-Many)
│       ├── LabResults (One-to-Many)
│       ├── InsuranceClaims (One-to-Many)
│       └── CareTeam (Many-to-Many)
│
├── Appointment__dlm (Custom DMO)
│   ├── Fields: AppointmentDate, Provider, Specialty, Status, NoShowFlag
│   └── Relationships:
│       └── Patient (Many-to-One)
│
├── Prescription__dlm (Custom DMO)
│   ├── Fields: Medication, Dosage, Frequency, PrescribedDate, RefillsRemaining
│   └── Relationships:
│       ├── Patient (Many-to-One)
│       └── Provider (Many-to-One)
│
├── LabResult__dlm (Custom DMO)
│   ├── Fields: TestType, ResultValue, ReferenceRange, OrderDate, ResultDate
│   └── Relationships:
│       └── Patient (Many-to-One)
│
└── Provider__dlm (Custom DMO)
    ├── Fields: ProviderName, Specialty, Location, Availability
    └── Relationships:
        └── Patients (One-to-Many)

Calculated Insights:
├── MedicationAdherence__ci (% prescriptions filled on time)
├── AppointmentNoShowRate__ci
├── ChronicDiseaseRiskScore__ci (based on labs, family history)
├── CareGapIdentification__ci (missing screenings, overdue checkups)
└── PreferredProvider__ci

Use Cases:
├── Proactive outreach for preventive care
├── Medication adherence programs
├── Care coordination across specialties
├── Population health management
└── Predictive analytics (hospital readmission risk)
```

### Financial Services: Customer 360 for Banking

```
Entities (DMOs):
├── Individual (Customer) - Standard DMO
│   ├── Custom Fields: CreditScore__c, RiskProfile__c, NetWorth__c
│   └── Relationships:
│       ├── Accounts (One-to-Many)
│       ├── Loans (One-to-Many)
│       ├── CreditCards (One-to-Many)
│       ├── Investments (One-to-Many)
│       └── Transactions (One-to-Many)
│
├── BankAccount__dlm (Custom DMO)
│   ├── Fields: AccountNumber, AccountType, Balance, OpenDate, Status
│   └── Relationships:
│       └── Owner (Many-to-One to Individual)
│
├── Loan__dlm (Custom DMO)
│   ├── Fields: LoanType, Principal, InterestRate, MonthlyPayment, OutstandingBalance
│   └── Relationships:
│       └── Borrower (Many-to-One)
│
├── CreditCard__dlm (Custom DMO)
│   ├── Fields: CardType, CreditLimit, Balance, APR, RewardsPoints
│   └── Relationships:
│       └── Cardholder (Many-to-One)
│
├── Transaction__dlm (Custom DMO)
│   ├── Fields: TransactionDate, Amount, Category, Merchant, Location
│   └── Relationships:
│       ├── Account (Many-to-One)
│       └── Individual (Many-to-One)
│
└── Investment__dlm (Custom DMO)
    ├── Fields: InvestmentType, CurrentValue, PurchaseDate, ReturnRate
    └── Relationships:
        └── Investor (Many-to-One)

Calculated Insights:
├── TotalAssets__ci (sum of all accounts + investments)
├── TotalLiabilities__ci (sum of loans + credit card balances)
├── NetWorth__ci (assets - liabilities)
├── MonthlySpendingByCategory__ci
├── DebtToIncomeRatio__ci
├── InvestmentRiskTolerance__ci (based on portfolio)
├── CrossSellOpportunity__ci (mortgage, investment accounts)
└── ChurnProbability__ci

Affinities:
├── Product Affinity (checking → savings → credit card → mortgage)
├── Channel Preference (mobile app, branch, online)
├── Service Time Preference (morning, evening, weekend)
└── Investment Type Preference (stocks, bonds, mutual funds)

Use Cases:
├── Personalized financial advice
├── Cross-sell/upsell optimization
├── Fraud detection (transaction patterns)
├── Credit risk assessment
└── Wealth management targeting
```

### Travel & Hospitality: Guest Experience Graph

```
Entities (DMOs):
├── Individual (Guest) - Standard DMO
│   ├── Custom Fields: LoyaltyStatus__c, TravelPreference__c, RoomPreference__c
│   └── Relationships:
│       ├── Reservations (One-to-Many)
│       ├── Stays (One-to-Many)
│       ├── LoyaltyAccount (One-to-One)
│       ├── Preferences (One-to-Many)
│       └── Reviews (One-to-Many)
│
├── Reservation__dlm (Custom DMO)
│   ├── Fields: CheckInDate, CheckOutDate, RoomType, RateCode, BookingChannel
│   └── Relationships:
│       ├── Guest (Many-to-One)
│       └── Property (Many-to-One)
│
├── Stay__dlm (Custom DMO)
│   ├── Fields: ActualCheckIn, ActualCheckOut, RoomNumber, TotalCharges, ServiceRequests
│   └── Relationships:
│       ├── Guest (Many-to-One)
│       ├── Reservation (One-to-One)
│       └── Services (One-to-Many) - spa, dining, etc.
│
├── Property__dlm (Custom DMO)
│   ├── Fields: PropertyName, Location, StarRating, Amenities
│   └── Relationships:
│       └── Reservations (One-to-Many)
│
├── GuestPreference__dlm (Custom DMO)
│   ├── Fields: PreferenceType, PreferenceValue, Priority
│   │   └── Examples: "Room Floor: High", "Pillow Type: Firm", "Diet: Vegetarian"
│   └── Relationships:
│       └── Guest (Many-to-One)
│
└── GuestReview__dlm (Custom DMO)
    ├── Fields: Rating, ReviewText, ReviewDate, AspectRatings (cleanliness, service, etc.)
    └── Relationships:
        ├── Guest (Many-to-One)
        └── Property (Many-to-One)

Calculated Insights:
├── LifetimeStays__ci
├── LifetimeRevenue__ci
├── AverageLengthOfStay__ci
├── PreferredProperty__ci
├── SeasonalTravelPattern__ci (when they typically travel)
├── BookingLeadTime__ci (how far in advance they book)
├── ServiceUtilizationScore__ci (spa, dining, concierge usage)
└── ReviewSentiment__ci

Affinities:
├── Property Affinity (which hotels they prefer)
├── Service Affinity (spa, restaurant, room service)
├── Seasonal Affinity (beach in summer, ski in winter)
└── Booking Channel Affinity (app, website, phone)

Use Cases:
├── Personalized pre-arrival communications
├── Room assignment optimization (based on preferences)
├── Service recommendations (spa, dining)
├── Dynamic pricing (based on booking patterns)
├── Loyalty program optimization
└── Targeted promotions for preferred destinations
```

---

## Conclusion

### Key Takeaways

```
Knowledge Graph Components:
├── Entities (DMOs) - Standard + Custom
│   └── Extended with custom fields for business needs
│
├── Relationships - Connect entities
│   └── Enable multi-hop queries and insights
│
├── Affinities - Calculated connections
│   └── Product, brand, channel, behavioral patterns
│
├── Calculated Insights - Derived metrics
│   └── Power segmentation and personalization
│
└── Real-time Activation - Put insights to work
    └── NBA, personalization, recommendations

Benefits:
├── 360-degree customer view
├── Predictive analytics
├── Personalization at scale
├── Cross-entity insights
├── Real-time decisioning
└── Improved customer experience

Implementation Best Practices:
├── Start with standard DMOs, extend as needed
├── Model real business relationships
├── Calculate insights incrementally
├── Test affinity algorithms on sample data
├── Document the knowledge graph structure
└── Monitor performance and optimize queries
```

### Next Steps

```
1. Identify Your Entities
   └── What are the key objects in your business?

2. Map Your Relationships
   └── How do entities connect?

3. Define Custom Fields
   └── What attributes are unique to your business?

4. Calculate Affinities
   └── What patterns matter most?

5. Build Insights
   └── What metrics drive decisions?

6. Activate
   └── How will you use the knowledge graph?
```

This knowledge graph approach transforms Data Cloud from a simple database into an intelligent customer data platform that understands connections, predicts behaviors, and enables truly personalized experiences at scale.
