# Data Cloud Governance Architecture: Tags, Metadata, Policies & Objects

## Quick Answer: Where Can Tags Be Applied?

Tags can be applied at **FOUR LEVELS**:

1. ✅ **Object Level** (DMO, DLO, External Objects)
2. ✅ **Field Level** (Individual columns/attributes)
3. ✅ **Row Level** (Individual records via classification)
4. ✅ **Value Level** (Specific data values via pattern matching)

---

## The Governance Hierarchy

```
ORGANIZATION
│
├── DATA SPACES (Logical partitions)
│   │
│   ├── OBJECTS (DMOs, DLOs)
│   │   │   [Tags: Object-level classification]
│   │   │   [Metadata: Object properties]
│   │   │   [Policies: Object access rules]
│   │   │
│   │   ├── FIELDS (Columns)
│   │   │   │   [Tags: Field-level classification]
│   │   │   │   [Metadata: Field properties]
│   │   │   │   [Policies: Field access rules]
│   │   │   │
│   │   │   └── VALUES (Data)
│   │   │       [Tags: Pattern-based classification]
│   │   │       [Metadata: Data lineage]
│   │   │       [Policies: Value-level rules]
│   │   │
│   │   └── ROWS (Records)
│   │       [Tags: Row classification]
│   │       [Metadata: Record properties]
│   │       [Policies: Row-level security]
│   │
│   └── GOVERNANCE OBJECTS
│       ├── Individuals (Unified profiles)
│       ├── Consent Records
│       ├── Segments
│       └── Activations
```

---

## Level 1: OBJECT-LEVEL Tags & Governance

### What Objects Can Be Tagged?

**Data Model Objects (DMOs)**
```
Object: Customer_DMO
Tags:
  - ObjectType: "DMO"
  - BusinessDomain: "Customer_360"
  - SensitivityLevel: "Confidential"
  - RegulatoryScope: ["GDPR", "CCPA"]
  - DataOwner: "Customer_Success_Team"
  - CriticalityLevel: "High"
  - RefreshFrequency: "Real-time"
```

**Data Lake Objects (DLOs)**
```
Object: WebClickstream_DLO
Tags:
  - ObjectType: "DLO"
  - SourceSystem: "Google_Analytics"
  - DataFormat: "Parquet"
  - SensitivityLevel: "Internal"
  - RetentionPeriod: "90_days"
  - PartitionStrategy: "Date"
```

**External Objects**
```
Object: SAP_Customer_External
Tags:
  - ObjectType: "External"
  - ExternalSystem: "SAP_ERP"
  - SyncFrequency: "Daily"
  - DataResidency: "EU"
```

### Object-Level Metadata

```json
{
  "objectApiName": "Customer_DMO",
  "objectLabel": "Customer Profile",
  "objectType": "DMO",
  "recordCount": 5000000,
  "storageSize": "2.5 TB",
  "createdDate": "2024-01-15T10:00:00Z",
  "lastModifiedDate": "2024-01-20T15:30:00Z",
  "isActive": true,
  "dataSpace": "Production",
  "tags": {
    "businessDomain": "Customer_360",
    "sensitivityLevel": "Confidential",
    "regulatoryScope": ["GDPR", "CCPA"]
  }
}
```

### Object-Level Policies

```
Policy: "Customer_DMO_Access_Control"
Scope: Customer_DMO object
Rules:
  - Sales_Users: Read access
  - Marketing_Users: Read access
  - Service_Users: Read + Edit access
  - Contractors: No access

Policy: "Customer_DMO_Encryption"
Scope: Customer_DMO object
Rules:
  - Encrypt at rest: AES-256
  - Encrypt in transit: TLS 1.3
  - Key rotation: Every 90 days
```

---

## Level 2: FIELD-LEVEL Tags & Governance

### What Fields Can Be Tagged?

**Every field in any object can have tags:**

```
Object: Customer_DMO

Field: Email
Tags:
  - ContentType: "PII"
  - SensitivityLevel: "Confidential"
  - Purpose: ["Marketing", "Transactional"]
  - RegulatoryTag: "GDPR"
  - ValidationRequired: "Email_Format"
  - MaskingRequired: "Non_Production"

Field: FirstName
Tags:
  - ContentType: "PII"
  - SensitivityLevel: "Internal"
  - Purpose: ["Marketing", "Service", "Transactional"]

Field: SSN
Tags:
  - ContentType: "SPI"
  - SensitivityLevel: "Restricted"
  - RegulatoryTag: ["HIPAA", "GDPR"]
  - EncryptionRequired: "Field_Level"
  - AccessRestriction: "Compliance_Team_Only"
  - AuditRequired: "All_Access"

Field: CustomerLifetimeValue
Tags:
  - ContentType: "Business_Metric"
  - SensitivityLevel: "Internal"
  - DataType: "Calculated"
  - Purpose: "Analytics"
  - RefreshFrequency: "Daily"
```

### Field-Level Metadata

```json
{
  "fieldApiName": "Email__c",
  "fieldLabel": "Email Address",
  "dataType": "Email",
  "length": 255,
  "isRequired": true,
  "isUnique": false,
  "isIndexed": true,
  "tags": {
    "contentType": "PII",
    "sensitivityLevel": "Confidential",
    "purpose": ["Marketing", "Transactional"]
  },
  "qualityMetrics": {
    "completeness": 98.5,
    "accuracy": 95.2,
    "uniqueness": 99.1
  },
  "governanceMetadata": {
    "encryptionEnabled": true,
    "maskingRule": "EmailMaskingRule",
    "retentionPeriod": 730,
    "legalBasis": "Consent"
  }
}
```

### Field-Level Policies

```
Policy: "Email_Field_Access"
Scope: Customer_DMO.Email field
Rules:
  - Marketing_Users: Read access
  - Sales_Users: Read access
  - Contractors: No access
  - Data_Exports: Require approval

Policy: "Email_Masking"
Scope: Customer_DMO.Email field
Rules:
  - Production: Show full value
  - Sandbox: Show as j***@***.com
  - Developer_Org: Show as xxxxx@xxxxx.xxx

Policy: "Email_Validation"
Scope: Customer_DMO.Email field
Rules:
  - Format: Must match email regex
  - Required: For active customers
  - On_Failure: Reject record
```

---

## Level 3: ROW-LEVEL Tags & Governance

### How Are Rows Tagged?

Rows are tagged **indirectly** through:
1. Classification algorithms
2. Field values that trigger tagging
3. Record-specific attributes

**Example: Individual Records**

```
Record ID: 001-12345
Individual: John Doe

Row-Level Classification:
Tags:
  - RecordType: "Customer"
  - LifecycleStage: "Active"
  - CustomerSegment: "VIP"
  - ConsentStatus: "Opted_In"
  - RiskLevel: "Low"
  - DataCompleteness: "95%"
  - GeographicRegion: "EU"
  - RegulatoryScope: "GDPR"
```

**How It Works:**

```
IF Individual.Country IN ['EU countries']
THEN add tag: "GDPR_Subject"

IF Individual.TotalSpend > 100000
THEN add tag: "VIP_Customer"

IF Individual.HasChildren = true
THEN add tag: "COPPA_Consideration"

IF Individual.ConsentStatus = "Opted_Out"
THEN add tag: "Do_Not_Contact"
```

### Row-Level Metadata

```json
{
  "recordId": "001-12345",
  "individualId": "IND-9876543",
  "recordType": "Customer",
  "tags": {
    "lifecycleStage": "Active",
    "segment": "VIP",
    "region": "EU",
    "consentStatus": "Opted_In"
  },
  "governanceAttributes": {
    "dataSubjectRights": ["Access", "Erasure", "Portability"],
    "consentRecords": [
      {
        "purpose": "Email_Marketing",
        "status": "Opted_In",
        "date": "2024-01-15"
      }
    ],
    "processingRestrictions": []
  },
  "qualityMetrics": {
    "completeness": 95,
    "lastVerified": "2024-01-18",
    "isGoldenRecord": true
  }
}
```

### Row-Level Policies (Row-Level Security)

```
Policy: "EU_Customers_Access"
Scope: Customer_DMO rows
Rules:
  - IF row.Country IN ['EU']
  - THEN visible to: EU_Team, Global_Admins
  - ELSE: Visible to all

Policy: "VIP_Customer_Access"
Scope: Customer_DMO rows
Rules:
  - IF row.CustomerTier = 'VIP'
  - THEN editable by: Account_Managers only
  - ELSE: Standard access

Policy: "Opted_Out_Restriction"
Scope: Customer_DMO rows
Rules:
  - IF row.ConsentStatus = 'Opted_Out'
  - THEN exclude from: Marketing_Segments
  - AND block: Email_Activations
```

---

## Level 4: VALUE-LEVEL Tags & Governance

### Pattern-Based Value Classification

Values are tagged based on **content patterns**:

```
Value: "john.doe@email.com"
Pattern: Email address
Tags Applied:
  - ValueType: "Email"
  - ContentClassification: "PII"
  - Format: "Valid_Email"

Value: "123-45-6789"
Pattern: SSN format
Tags Applied:
  - ValueType: "SSN"
  - ContentClassification: "SPI"
  - RegulatoryScope: "HIPAA"
  - RequiresEncryption: true
  - AutoMaskInLogs: true

Value: "4532-1234-5678-9010"
Pattern: Credit card
Tags Applied:
  - ValueType: "CreditCard"
  - ContentClassification: "PCI"
  - EncryptionRequired: "PCI_DSS"
  - LoggingProhibited: true
  - RetentionRestriction: "Minimize"
```

### Value-Level Policies

```
Policy: "SSN_Detection_and_Protection"
Trigger: Value matches SSN pattern
Actions:
  - Auto-classify as "Restricted"
  - Enable field-level encryption
  - Apply masking in non-production
  - Audit all access
  - Block from exports without approval

Policy: "Email_Format_Enforcement"
Trigger: Field tagged as "Email"
Actions:
  - Validate format on ingestion
  - Normalize to lowercase
  - Check against disposable email list
  - Flag suspicious domains
```

---

## The Relationships: How Everything Connects

### Tags ↔ Metadata Relationship

```
METADATA CONTAINS TAGS

Object Metadata:
{
  "name": "Customer_DMO",
  "recordCount": 5000000,
  "storageSize": "2.5 TB",
  "tags": {                          ← Tags are embedded in metadata
    "sensitivityLevel": "Confidential",
    "businessDomain": "Sales"
  }
}

Tags are stored AS metadata
Metadata is the container
Tags are specific classification metadata
```

**Think of it this way:**
- **Metadata** = All descriptive information about an asset
- **Tags** = Specific classification/categorization metadata
- Tags are a **subset** of metadata

### Tags → Policies Relationship

```
TAGS TRIGGER POLICIES

Tag: "PII"
  ↓
Triggers Policies:
  → EncryptionPolicy
  → MaskingPolicy
  → AccessControlPolicy
  → AuditPolicy

Tag: "GDPR"
  ↓
Triggers Policies:
  → ConsentEnforcementPolicy
  → DataSubjectRightsPolicy
  → RetentionPolicy
  → CrossBorderTransferPolicy
```

**Flow:**
```
1. Data arrives
2. System/User applies TAGS (classification)
3. Tags trigger POLICIES (enforcement)
4. Policies control behavior
5. Actions logged in METADATA
```

### Policies ↔ Governance Objects Relationship

```
POLICIES USE GOVERNANCE OBJECTS

Policy: "Consent_Enforcement_Policy"
Uses Governance Objects:
  → Consent Object (check consent status)
  → Individual Object (identify person)
  → Data Purpose Object (validate purpose)

Flow:
1. User tries to activate segment
2. Policy checks Consent Object
3. If consented → Allow
4. If not → Block
5. Log action in Audit Object
```

### Complete Relationship Diagram

```
┌─────────────────────────────────────────────────────┐
│                   DATA ASSET                         │
│              (Object, Field, Row, Value)             │
└──────────────────┬──────────────────────────────────┘
                   │
        ┌──────────┴───────────┐
        │                      │
        ▼                      ▼
┌───────────────┐      ┌──────────────┐
│   METADATA    │      │     TAGS     │
│  (Properties) │◄─────┤(Classification)│
└───────┬───────┘      └──────┬───────┘
        │                     │
        │                     │ (Triggers)
        │                     ▼
        │              ┌──────────────┐
        │              │   POLICIES   │
        │              │ (Enforcement) │
        │              └──────┬───────┘
        │                     │
        │                     │ (Uses)
        │                     ▼
        │              ┌──────────────────┐
        └─────────────►│GOVERNANCE OBJECTS│
                       │  (Consent, etc)  │
                       └──────────────────┘
```

---

## Practical Examples: Everything Together

### Example 1: Email Field - Complete Governance

**1. Field Definition**
```
Object: Customer_DMO
Field: Email
Type: String(255)
```

**2. Field-Level Tags (Classification)**
```
Tags:
  - ContentType: "PII"
  - SensitivityLevel: "Confidential"
  - Purpose: "Marketing"
  - RegulatoryTag: "GDPR"
```

**3. Field-Level Metadata (Properties)**
```
Metadata:
  - CreatedDate: "2024-01-15"
  - IsIndexed: true
  - IsEncrypted: true
  - MaskingRule: "EmailMask"
  - QualityScore: 95.5
  - Completeness: 98%
```

**4. Policies Triggered by Tags (Enforcement)**
```
Because tag = "PII":
  → Policy: Field-level encryption enabled
  → Policy: Access restricted by role
  → Policy: Masked in sandbox

Because tag = "Marketing":
  → Policy: Consent required for use
  → Policy: Cannot share with third parties without opt-in

Because tag = "GDPR":
  → Policy: Data subject rights apply
  → Policy: Retention limited to 2 years
  → Policy: Cross-border transfer restrictions
```

**5. Governance Objects Used (Runtime)**
```
When accessing email field:
  → Check Individual Object (who is this?)
  → Check Consent Object (did they consent?)
  → Check Data Purpose Object (allowed use?)
  → Log to Audit Object (record access)
```

**6. Row-Level Application**
```
Record: john.doe@email.com
  → Value-level tag: "Valid_Email"
  → Row-level tag: "GDPR_Subject" (if EU resident)
  → Individual-level: ConsentStatus = "Opted_In"
  → Policy check: Allow use for marketing
```

---

### Example 2: Customer Object - Multi-Level Governance

**Object Level:**
```
Object: Customer_DMO
Tags:
  - BusinessDomain: "Customer_360"
  - SensitivityLevel: "Confidential"
  - CriticalityLevel: "High"

Policies Applied:
  - Access: Sales, Marketing, Service teams only
  - Encryption: All data at rest
  - Backup: Daily snapshots
  - Retention: 7 years
```

**Field Level:**
```
Field: SSN
Tags:
  - ContentType: "SPI"
  - SensitivityLevel: "Restricted"
  - RegulatoryTag: "HIPAA"

Policies Applied:
  - Access: Compliance team only
  - Encryption: Field-level AES-256
  - Masking: XXX-XX-1234 in all environments
  - Audit: Log every access
  - Export: Blocked

Field: Email
Tags:
  - ContentType: "PII"
  - SensitivityLevel: "Confidential"
  - Purpose: "Marketing"

Policies Applied:
  - Access: Marketing + Sales teams
  - Masking: Partial in sandbox
  - Consent: Required for marketing use
  - Validation: Email format check
```

**Row Level:**
```
Row: Customer #12345
Tags (Auto-Applied):
  - Region: "EU"
  - ConsentStatus: "Opted_In"
  - CustomerTier: "VIP"

Policies Applied:
  - GDPR rights enabled
  - Account manager access only
  - Priority support queue
  - Enhanced data protection
```

**Value Level:**
```
Value: "john.doe@company.com"
Pattern Detection:
  - Detected as: Corporate email

Tags Applied:
  - EmailType: "Corporate"
  - ValidFormat: true
  - Deliverable: true

Policies Applied:
  - Higher priority for B2B campaigns
  - Different validation rules
```

---

## Tag Inheritance & Propagation

### How Tags Flow Through the System

**1. Top-Down Inheritance**
```
Object tagged as "GDPR"
  ↓
All fields inherit "GDPR" tag
  ↓
All rows inherit "GDPR" tag
  ↓
All values subject to GDPR policies

Override allowed: Field can be tagged "GDPR_Exempt"
```

**2. Bottom-Up Propagation**
```
Field contains SSN (value-level detection)
  ↓
Field auto-tagged as "SPI"
  ↓
Object marked as "Contains_SPI"
  ↓
Object-level policies adjusted
```

**3. Horizontal Propagation (Lineage)**
```
Source Field: Email (tagged "PII")
  ↓ (transformation)
Calculated Field: Email_Domain
  ↓ (inherits)
Tags: "PII_Derived", "Marketing"
  ↓ (flows to)
All downstream uses maintain tags
```

---

## Metadata Types vs Tags vs Policies

### Clear Distinctions

| Aspect | Metadata | Tags | Policies |
|--------|----------|------|----------|
| **Purpose** | Describe properties | Classify/categorize | Enforce rules |
| **Nature** | Comprehensive info | Specific labels | Action rules |
| **Examples** | CreatedDate, RecordCount, Size | PII, Confidential, GDPR | EncryptionRequired, AccessControl |
| **Scope** | Everything about asset | Classification aspects | Behavioral controls |
| **Changes** | Frequently (properties update) | Occasionally (reclassification) | Rarely (rule changes) |
| **Applied By** | System (mostly automatic) | System + Users | Admins/Governance team |
| **Stored As** | Structured fields | Key-value pairs (subset of metadata) | Rule definitions |
| **Effect** | Information only | Triggers policies | Enforces behavior |

### How They Work Together

```
SCENARIO: New customer record created

Step 1: System creates METADATA
  - RecordId: 001-98765
  - CreatedDate: 2024-01-20T10:30:00Z
  - CreatedBy: WebForm_Integration
  - RecordCount: Object now has 5,000,001 records
  - StorageSize: Object now 2.501 TB

Step 2: System/User applies TAGS
  - Based on Country field → Tag: "GDPR_Subject"
  - Based on Email field → Tag: "PII_Present"
  - Based on ConsentCheckbox → Tag: "Consent_Opted_In"
  - Based on Source → Tag: "Web_Signup"

Step 3: Tags trigger POLICIES
  - "GDPR_Subject" tag → Apply GDPR_Compliance_Policy
  - "PII_Present" tag → Apply Encryption_Policy
  - "Consent_Opted_In" tag → Allow Marketing_Use_Policy
  - "Web_Signup" tag → Apply Web_Validation_Policy

Step 4: Policies interact with GOVERNANCE OBJECTS
  - Create Consent Object (purpose: Marketing)
  - Link to Individual Object (create or update)
  - Update Audit Object (log creation)
  - Check against Segment Objects (auto-include?)

Step 5: Results stored in METADATA
  - LastModifiedDate updated
  - QualityScore calculated: 92
  - ComplianceStatus: "Verified"
  - PolicyChecks: All passed
```

---

## Governance Object Relationships

### Core Governance Objects and Their Connections

```
┌──────────────┐
│  INDIVIDUAL  │ (Central object)
└──────┬───────┘
       │
       ├─────► CONSENT RECORDS (1:many)
       │       │
       │       └─────► DATA PURPOSE (many:1)
       │
       ├─────► DATA SUBJECT RIGHTS REQUESTS (1:many)
       │
       ├─────► INDIVIDUAL SOURCES (1:many)
       │       │
       │       └─────► SOURCE SYSTEMS (many:1)
       │
       ├─────► SEGMENT MEMBERSHIP (many:many)
       │       │
       │       └─────► SEGMENTS (many:1)
       │
       └─────► ACTIVATION HISTORY (1:many)
               │
               └─────► ACTIVATION TARGETS (many:1)
```

### How Tags/Policies Apply to Each

**Individual Object**
```
Tags:
  - LifecycleStage
  - CustomerTier
  - Region
  - ConsentSummary

Metadata:
  - IndividualId
  - SourceCount
  - DataCompletenessScore
  - LastEngagementDate

Policies:
  - Access control by region
  - Data subject rights enforcement
  - Consent validation on use
```

**Consent Object**
```
Tags:
  - Purpose (Marketing, Analytics, etc.)
  - Channel (Email, SMS, Phone)
  - Status (Opted-in, Opted-out)

Metadata:
  - ConsentDate
  - ConsentMethod
  - ExpirationDate
  - LegalBasis

Policies:
  - Expiration enforcement
  - Withdrawal processing
  - Audit all changes
```

**Segment Object**
```
Tags:
  - SegmentType (Static, Dynamic)
  - BusinessPurpose
  - ActivationStatus

Metadata:
  - MemberCount
  - LastRefreshDate
  - CreatedBy
  - ApprovalStatus

Policies:
  - Consent filtering required
  - Refresh schedule enforcement
  - Activation approval workflow
```

---

## Quick Reference: Tag Application Matrix

| Level | What Gets Tagged | Tag Examples | Policy Examples |
|-------|------------------|--------------|-----------------|
| **Organization** | Entire Data Cloud instance | MultiRegion, Industry, ComplianceLevel | Global_Encryption, Org_Retention |
| **Data Space** | Logical partition | Production, Development, Regional | Space_Access_Control, Space_Isolation |
| **Object** | DMO, DLO, External | BusinessDomain, Criticality, SourceSystem | Object_Access, Object_Encryption |
| **Field** | Individual columns | PII, SPI, Purpose, Sensitivity | Field_Masking, Field_Access, Validation |
| **Row** | Individual records | Region, ConsentStatus, Tier | Row_Level_Security, Regional_Access |
| **Value** | Specific data | Pattern_Detected, Format_Valid | Value_Masking, Pattern_Blocking |

---

## Summary: The Mental Model

### Think of Governance as a Layered Cake

```
┌─────────────────────────────────────┐
│         GOVERNANCE LAYER            │ ← Policies enforce rules
├─────────────────────────────────────┤
│      CLASSIFICATION LAYER           │ ← Tags classify data
├─────────────────────────────────────┤
│       DESCRIPTION LAYER             │ ← Metadata describes data
├─────────────────────────────────────┤
│          DATA LAYER                 │ ← Actual data (objects, fields, rows)
└─────────────────────────────────────┘
```

### The Flow

```
1. DATA exists (objects, fields, rows, values)
   ↓
2. METADATA describes it (properties, statistics)
   ↓
3. TAGS classify it (PII, Confidential, GDPR)
   ↓
4. POLICIES enforce rules based on tags
   ↓
5. GOVERNANCE OBJECTS track compliance
   ↓
6. Results update METADATA (audit trail)
```

### Key Relationships

**Metadata ⊃ Tags**
- Tags are a type of metadata
- All tags are metadata, not all metadata are tags

**Tags → Policies**
- Tags trigger policies
- Policies reference tags
- One tag can trigger multiple policies

**Policies ↔ Governance Objects**
- Policies use governance objects for decisions
- Governance objects enforce policy rules
- Bidirectional relationship

**Everything → Metadata**
- All actions update metadata
- Metadata provides audit trail
- Metadata enables reporting

---

*This architecture guide provides the complete mental model for how tags, metadata, policies, and governance objects relate in Data Cloud.*

*Last updated: 2025*
