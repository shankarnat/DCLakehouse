# Salesforce & Data Cloud: Licenses, Permissions & Permission Sets Guide

## Quick Mental Model

Think of it like a building with security:

```
LICENSE = Your building access badge (what you paid for)
  ↓
PROFILE = Your default floor/room access (baseline permissions)
  ↓
PERMISSION SETS = Special keycards (additional access beyond profile)
  ↓
ACTUAL ACCESS = Profile + All Permission Sets combined
```

---

## Table of Contents
1. [Licenses](#licenses)
2. [Permissions](#permissions)
3. [Profiles](#profiles)
4. [Permission Sets](#permission-sets)
5. [Data Cloud Specifics](#data-cloud-specifics)
6. [How They Work Together](#how-they-work-together)
7. [Real-World Examples](#real-world-examples)

---

## LICENSES

### What is a License?

A **license** is what you **purchase** from Salesforce. It determines:
- Which Salesforce products you can access
- How many users can access them
- What features are available

**Key Concept:** A license is the **prerequisite** for access. Without a license, you can't even log in.

---

### Types of Licenses

#### **1. Salesforce Platform Licenses**

**Salesforce License (Full CRM)**
```
What you get:
- Full Salesforce CRM access
- Sales Cloud features
- Service Cloud features
- Standard objects (Account, Contact, Opportunity, Case)
- Custom objects (unlimited)
- API access
- Reports & Dashboards

Cost: ~$150-300/user/month
User example: Sales reps, account managers
```

**Salesforce Platform License**
```
What you get:
- Custom objects only (no standard CRM objects)
- Custom apps
- Limited standard objects access
- API access
- Good for internal apps

Cost: ~$25/user/month
User example: Internal operations staff
```

**Chatter Free / Chatter Plus**
```
What you get:
- Chatter collaboration only
- Limited object access
- No API access

Cost: Free or ~$15/user/month
User example: External collaborators
```

#### **2. Data Cloud Licenses**

**Data Cloud License**
```
What you get:
- Access to Data Cloud features
- Identity resolution
- Segmentation
- Activation capabilities
- Query/analytics on unified data
- Credits for operations

IMPORTANT: This is ADD-ON to Salesforce license!

Cost: Included in package OR ~$1,000-2,000/user/month
User example: Data analysts, marketers
```

**Einstein 1 Edition (Bundled)**
```
What you get:
- Salesforce CRM license
- Data Cloud included
- Einstein AI features
- Unified platform

Cost: Custom pricing
User example: Full-featured users
```

---

### License Assignment Model

```
ORGANIZATION (Company)
  │
  ├─ Purchased Licenses:
  │   ├─ 100 Salesforce Licenses
  │   ├─ 20 Data Cloud Licenses
  │   └─ 50 Platform Licenses
  │
  └─ Users (must be assigned a license):
      ├─ User 1: Salesforce + Data Cloud
      ├─ User 2: Salesforce only
      ├─ User 3: Data Cloud only (rare)
      └─ User 4: Platform only
```

**Key Point:** Each user must have at least ONE license. Some users can have multiple.

---

## PERMISSIONS

### What are Permissions?

**Permissions** are granular controls that define what a user can **DO** within Salesforce.

**Three levels of permissions:**

```
1. OBJECT-LEVEL PERMISSIONS (Can you access this object?)
   - Create, Read, Edit, Delete on objects
   - View All, Modify All

2. FIELD-LEVEL PERMISSIONS (Can you see/edit this field?)
   - Read access to field
   - Edit access to field

3. SYSTEM PERMISSIONS (Can you do this system action?)
   - Modify All Data
   - View Setup
   - Manage Users
   - API Enabled
```

---

### Object-Level Permissions (CRUD)

```
Object: Account

Permissions:
  ✓ Create (C) - Can create new accounts
  ✓ Read (R) - Can view accounts
  ✓ Update (U) - Can edit accounts
  ✗ Delete (D) - Cannot delete accounts

Special:
  ✗ View All - Cannot see all accounts (RLS applies)
  ✗ Modify All - Cannot edit all accounts
```

**How it works:**
- User can only perform actions they have permissions for
- Permissions combined with sharing rules determine actual access

---

### Field-Level Security (FLS)

```
Object: Account
Field: Annual Revenue

Permissions:
  ✓ Read - Can see the Annual Revenue value
  ✗ Edit - Cannot change the Annual Revenue value

Result: Field is visible but read-only for this user
```

**How it works:**
- Even if you can edit an Account, specific fields might be restricted
- FLS overrides object permissions (more restrictive wins)

---

### System Permissions

```
Common System Permissions:

✓ API Enabled - Can use API integrations
✓ Run Reports - Can execute reports
✗ Modify All Data - Cannot bypass security
✗ View Setup - Cannot access Setup menu
✗ Manage Users - Cannot create/edit users
```

**Data Cloud System Permissions:**
```
✓ Data Cloud User - Can access Data Cloud interface
✓ Query Data Cloud - Can run queries
✗ Manage Data Cloud - Cannot configure Data Cloud
✗ View All Data Cloud Records - Limited by RLS
```

---

## PROFILES

### What is a Profile?

A **profile** is a collection of permissions that defines a user's **baseline access**.

**Key Characteristics:**
- Every user must have EXACTLY ONE profile
- Profiles are BROAD (apply to many users)
- Profiles are HARD TO CHANGE (affects all users with that profile)
- Profiles define MINIMUM permissions

---

### Standard Profiles

Salesforce provides standard profiles:

```
System Administrator
├─ Object: ALL objects (CRUD + View All + Modify All)
├─ Fields: ALL fields (Read + Edit)
├─ System: ALL system permissions
└─ Use Case: Admins, developers

Standard User
├─ Object: Standard objects (CRUD, no View/Modify All)
├─ Fields: Most fields (Read + Edit)
├─ System: Basic permissions (API, Reports)
└─ Use Case: Sales reps, service agents

Minimum Access
├─ Object: Limited access
├─ Fields: Minimal access
├─ System: Very limited
└─ Use Case: External users, contractors
```

---

### Custom Profiles

Organizations create custom profiles:

```
Sales Manager Profile
├─ Object Permissions:
│   ├─ Account: CRUD + View All
│   ├─ Opportunity: CRUD + View All
│   ├─ Lead: CRUD + View All
│   └─ Contact: CRUD
├─ Field Permissions:
│   ├─ Account.Revenue: Read + Edit
│   ├─ Opportunity.Amount: Read + Edit
│   └─ Account.Owner: Read only
├─ System Permissions:
│   ├─ API Enabled: Yes
│   ├─ Run Reports: Yes
│   └─ Manage Users: No
└─ Assigned To: All sales managers (20 users)
```

**Key Point:** Changing this profile affects ALL 20 users immediately.

---

### Profile Limitations

**Problem with Profiles:**
```
Scenario: Sales rep needs temporary access to forecasting

Bad Solution:
1. Create new profile "Sales Rep + Forecasting"
2. Move user to new profile
3. User loses some permissions, gains others
4. Complex to manage

Problem: Profile explosion (too many variations)
```

**This is why Permission Sets were invented!**

---

## PERMISSION SETS

### What is a Permission Set?

A **permission set** is a bundle of permissions that can be **assigned to any user** to give them **additional access** beyond their profile.

**Key Characteristics:**
- Users can have ZERO or MANY permission sets
- Permission sets are ADDITIVE ONLY (never remove permissions)
- Permission sets are FLEXIBLE (easy to assign/remove)
- Permission sets are TARGETED (specific features/functions)

---

### Permission Sets vs Profiles

| Aspect | Profile | Permission Set |
|--------|---------|----------------|
| **Required?** | YES - every user has 1 | NO - users can have 0 to many |
| **How many?** | Exactly 1 per user | 0 to unlimited per user |
| **Effect** | Defines baseline | Adds to baseline |
| **Can remove permissions?** | Yes (defines what you have) | NO (only grants more) |
| **Best for** | Job role (Sales, Service) | Specific feature access |
| **Example** | "Sales User" | "Data Cloud Analyst" |

---

### Permission Set Example

```
Permission Set: "Data Cloud Query User"

Object Permissions:
  ✓ Customer_DMO: Read
  ✓ Order_DMO: Read
  ✗ All other DMOs: No access

System Permissions:
  ✓ Data Cloud User
  ✓ Query Data Cloud
  ✗ Manage Data Cloud

Assigned To:
  ✓ User: jane@company.com (Sales Manager)
  ✓ User: bob@company.com (Marketing Analyst)
  ✓ User: alice@company.com (Product Manager)

Result:
- Jane keeps all Sales Manager profile permissions
- Jane GAINS Data Cloud query access
- Jane can now query Customer_DMO and Order_DMO
```

---

### Permission Set Groups

**Permission Set Groups** bundle multiple permission sets together.

```
Permission Set Group: "Data Cloud Analyst"

Includes:
  1. Data Cloud User (basic access)
  2. Data Cloud Query (run queries)
  3. Data Cloud Segmentation (create segments)
  4. DMO Reader (read all DMOs)

Assign Group → User gets all 4 permission sets

Use Case: Instead of assigning 4 individual permission sets,
          assign 1 group = simpler management
```

---

## DATA CLOUD SPECIFICS

### Data Cloud License Requirements

**To use Data Cloud, you need:**

```
Option 1: Salesforce License + Data Cloud Add-On
  ├─ Salesforce License: $150/user/month
  ├─ Data Cloud Add-On: $1,000/user/month
  └─ Total: $1,150/user/month

Option 2: Einstein 1 Edition (Bundled)
  ├─ Salesforce + Data Cloud + AI included
  └─ Total: Custom pricing (usually cheaper than separate)

Option 3: Data Cloud Only (Rare)
  ├─ Data Cloud license without full Salesforce
  ├─ Limited to Data Cloud features only
  └─ Cannot access standard CRM objects
```

---

### Data Cloud Permission Sets

**Standard Data Cloud Permission Sets:**

#### **1. Data Cloud User**
```
Purpose: Basic Data Cloud access
Permissions:
  ✓ Access Data Cloud app
  ✓ View Data Cloud home
  ✗ No query or management access

Assigned To: Everyone who needs to see Data Cloud exists
```

#### **2. Data Cloud Query User**
```
Purpose: Run queries on data
Permissions:
  ✓ Data Cloud User (includes above)
  ✓ Query Data Cloud objects
  ✓ View query results
  ✗ Cannot create DMOs/DLOs

Assigned To: Analysts, data scientists
```

#### **3. Data Cloud Segmentation User**
```
Purpose: Create marketing segments
Permissions:
  ✓ Data Cloud Query User (includes above)
  ✓ Create segments
  ✓ Edit segments
  ✓ View segment membership
  ✗ Cannot activate segments

Assigned To: Marketing campaign managers
```

#### **4. Data Cloud Activation User**
```
Purpose: Activate segments to external systems
Permissions:
  ✓ Data Cloud Segmentation User (includes above)
  ✓ Activate segments
  ✓ Configure destinations
  ✓ View activation history

Assigned To: Marketing operations, campaign executors
```

#### **5. Data Cloud Admin**
```
Purpose: Full Data Cloud administration
Permissions:
  ✓ ALL above permissions
  ✓ Manage Data Cloud configuration
  ✓ Create/modify DMOs and DLOs
  ✓ Configure identity resolution
  ✓ Manage data sources
  ✓ Configure consent management

Assigned To: Data Cloud administrators, data engineers
```

---

### Custom Data Cloud Permission Sets

Organizations create custom permission sets:

```
Custom Permission Set: "Marketing_Data_Analyst"

Object Permissions:
  ✓ Customer_DMO: Read
  ✓ Engagement_DMO: Read
  ✓ Campaign_DMO: Read
  ✗ Order_DMO: No access (Sales data)
  ✗ Support_Case_DMO: No access (Service data)

System Permissions:
  ✓ Data Cloud User
  ✓ Query Data Cloud
  ✓ Create Segments

Field-Level Security:
  ✓ Customer_DMO.Email: Read
  ✓ Customer_DMO.Name: Read
  ✗ Customer_DMO.SSN: No access
  ✗ Customer_DMO.CreditScore: No access

Rationale: Marketing analysts need customer data for
           campaigns but shouldn't see sensitive PII
```

---

## HOW THEY WORK TOGETHER

### The Complete Access Model

```
USER'S TOTAL ACCESS = LICENSE + PROFILE + ALL PERMISSION SETS

Example User: jane@company.com

LICENSE:
  ├─ Salesforce License (full CRM)
  └─ Data Cloud Add-On

PROFILE: "Sales Manager"
  ├─ Account: CRUD + View All
  ├─ Opportunity: CRUD + View All
  ├─ Contact: CRUD
  └─ API Enabled: Yes

PERMISSION SET 1: "Data Cloud Query User"
  ├─ Data Cloud User: Yes
  ├─ Query Data Cloud: Yes
  └─ Customer_DMO: Read

PERMISSION SET 2: "Forecasting Access"
  ├─ Forecasting: Full access
  └─ Forecast_Manager: Yes

FINAL ACCESS:
  ✓ All Sales Manager permissions (from profile)
  ✓ Data Cloud query access (from perm set 1)
  ✓ Forecasting access (from perm set 2)
  ✓ Can query Customer_DMO in Data Cloud
  ✓ Can manage sales forecasts
```

---

### Access Calculation Flow

```
STEP 1: Check License
─────────────────────────
Question: Does user have required license?
  - Salesforce License? ✓
  - Data Cloud License? ✓
If NO → Access DENIED (cannot proceed)
If YES → Proceed to Step 2

STEP 2: Check Profile
─────────────────────────
Question: What baseline permissions from profile?
  - Profile: "Sales Manager"
  - Has Account access? ✓
  - Has API access? ✓
Baseline permissions established

STEP 3: Add Permission Sets
─────────────────────────
Question: What additional permissions from permission sets?
  - Permission Set 1: Data Cloud Query User
    → ADDS: Query Data Cloud
  - Permission Set 2: Forecasting Access
    → ADDS: Forecasting access
Additional permissions granted

STEP 4: Apply Field-Level Security
─────────────────────────
Question: Can user access specific fields?
  - Profile says: Account.Revenue = Read + Edit ✓
  - Permission Set says: Customer_DMO.Email = Read ✓
  - No permission set grants SSN access ✗
Field-level access determined

STEP 5: Apply Sharing Rules (Row-Level Security)
─────────────────────────
Question: Which specific records can user see?
  - Profile/PermSet grant object access
  - Sharing rules determine which records
  - User sees only owned + shared records

FINAL RESULT: User's actual access determined
```

---

### The "OR" Logic (Most Permissive Wins)

**Key Principle:** If ANY source grants permission, user has that permission.

```
Example: Account Object Access

Profile: "Standard User"
  ├─ Account: Read, Edit
  └─ View All: NO

Permission Set: "Account Viewer"
  ├─ Account: Read
  └─ View All: YES

Result: User has Read + Edit + View All ✓
(Combines both, takes most permissive)
```

**Another Example: Field Access**

```
Field: Account.AnnualRevenue

Profile: "Sales User"
  └─ AnnualRevenue: Read only

Permission Set: "Revenue Manager"
  └─ AnnualRevenue: Read + Edit

Result: User can Read + Edit ✓
(Permission set grants additional Edit permission)
```

---

### The "AND" Logic (Most Restrictive Wins)

**When does restrictive logic apply?**

**Sharing Rules (Row-Level):**
```
Profile grants: Can Read all Accounts
Sharing Rule: Can only see Accounts in your region

Result: Can only see Accounts in your region ✗
(Sharing restricts what profile grants)
```

**License Restrictions:**
```
Profile grants: Full Salesforce access
License: Platform License (no standard objects)

Result: Cannot access standard objects ✗
(License restricts what profile grants)
```

---

## REAL-WORLD EXAMPLES

### Example 1: Marketing Team Setup

**Requirement:** Marketing team needs Data Cloud access with different levels

#### **Setup:**

**All Marketing Users Get:**
```
License: Salesforce License + Data Cloud Add-On
Profile: "Marketing User"
  ├─ Campaign: CRUD
  ├─ Lead: CRUD
  ├─ Contact: Read only
  └─ Account: Read only
```

**Tiered Access via Permission Sets:**

**Marketing Analyst (Entry Level):**
```
Permission Sets:
  1. "Data Cloud User" (basic access)
  2. "Data Cloud Query User" (run queries)
  3. "Marketing DMO Reader" (read marketing DMOs)

Can Do:
  ✓ View Data Cloud
  ✓ Run queries on Customer_DMO, Engagement_DMO
  ✓ View segment results
  ✗ Cannot create segments
  ✗ Cannot activate
```

**Campaign Manager (Mid Level):**
```
Permission Sets:
  1. "Data Cloud User"
  2. "Data Cloud Query User"
  3. "Data Cloud Segmentation User"
  4. "Marketing DMO Reader"

Can Do:
  ✓ Everything analysts can do
  ✓ Create segments
  ✓ Edit segments
  ✓ Preview segment members
  ✗ Cannot activate to external systems
```

**Marketing Operations (Senior Level):**
```
Permission Sets:
  1. "Data Cloud User"
  2. "Data Cloud Query User"
  3. "Data Cloud Segmentation User"
  4. "Data Cloud Activation User"
  5. "Marketing DMO Reader"

Can Do:
  ✓ Everything campaign managers can do
  ✓ Activate segments to Mailchimp, Facebook, Google Ads
  ✓ Configure destination connections
  ✓ View activation history
  ✗ Cannot manage Data Cloud configuration
```

**Marketing Data Architect (Admin Level):**
```
Permission Sets:
  1. "Data Cloud Admin" (includes all above)
  2. "Marketing DMO Full Access"

Can Do:
  ✓ Everything operations can do
  ✓ Create/modify DMOs and DLOs
  ✓ Configure identity resolution
  ✓ Manage data sources
  ✓ Configure consent management
```

**Benefits of This Approach:**
- Single "Marketing User" profile for all
- Permission sets provide tiered access
- Easy to promote users (add permission set)
- Easy to revoke access (remove permission set)
- No need for multiple profiles

---

### Example 2: Contractor with Temporary Access

**Requirement:** Contractor needs Data Cloud query access for 3 months

#### **Setup:**

```
User: contractor@external.com

License: Salesforce License (company provided)
Profile: "Minimum Access"
  ├─ Very limited baseline
  ├─ No standard object access
  └─ No system permissions

Permission Set: "Contractor_Data_Cloud_Query"
  ├─ Data Cloud User
  ├─ Query Data Cloud
  ├─ Customer_DMO: Read only
  ├─ Order_DMO: Read only
  └─ Expiration Date: 2024-04-30

Result:
  ✓ Contractor can query specific DMOs
  ✓ No access to CRM data
  ✓ No access to sensitive fields (FLS applied)
  ✓ Permission set auto-revoked after 3 months
```

**After 3 months:**
```
Permission Set expires → Contractor loses Data Cloud access
Profile remains "Minimum Access" → Almost no permissions
User account remains → Can be reactivated if needed

Security maintained without manual intervention!
```

---

### Example 3: Cross-Functional Project Team

**Requirement:** Temporary project team from Sales, Marketing, Service needs shared access

#### **Team Members:**

**Sales Rep:**
```
License: Salesforce License + Data Cloud
Profile: "Sales User"
  ├─ Account, Opportunity, Lead: CRUD
  └─ Normal sales permissions

Added Permission Set: "Project_X_Data_Access"
  ├─ Special_Project_DMO: Read + Edit
  ├─ Project_Dashboard: View
  └─ Cross_Functional_Report: Run

Result:
  ✓ Keeps all sales permissions
  ✓ Gains project-specific access
  ✓ Can collaborate on shared data
```

**Marketing User:**
```
Profile: "Marketing User"
  ├─ Campaign, Lead: CRUD
  └─ Normal marketing permissions

Added Permission Set: "Project_X_Data_Access" (SAME ONE!)
  ├─ Special_Project_DMO: Read + Edit
  ├─ Project_Dashboard: View
  └─ Cross_Functional_Report: Run

Result:
  ✓ Keeps all marketing permissions
  ✓ Gains project-specific access
  ✓ Same shared access as sales rep
```

**Service Agent:**
```
Profile: "Service User"
  ├─ Case, Contact: CRUD
  └─ Normal service permissions

Added Permission Set: "Project_X_Data_Access" (SAME ONE!)

Result:
  ✓ Keeps all service permissions
  ✓ Gains project-specific access
```

**Benefits:**
- Single permission set for entire project team
- Each user keeps their day-to-day permissions
- Project ends? Remove permission set from all → Access revoked
- No profile changes needed

---

## BEST PRACTICES

### 1. License Management

**DO:**
```
✓ Audit licenses quarterly
✓ Assign appropriate license tier (don't overpay)
✓ Use Platform licenses for internal users
✓ Bundle Data Cloud with Salesforce when possible (cheaper)
✓ Track unused licenses
```

**DON'T:**
```
✗ Give everyone Salesforce License "just in case"
✗ Buy Data Cloud licenses for users who won't use it
✗ Leave inactive users with licenses assigned
✗ Forget to remove licenses when users leave
```

### 2. Profile Strategy

**DO:**
```
✓ Keep profiles BROAD (Sales, Marketing, Service)
✓ Limit number of profiles (< 10 if possible)
✓ Use standard profiles when possible
✓ Document profile purposes clearly
```

**DON'T:**
```
✗ Create profile variations for small differences
✗ Create user-specific profiles
✗ Use profiles for temporary access
✗ Create "Sales_Rep_With_Forecasting" type profiles
```

### 3. Permission Set Strategy

**DO:**
```
✓ Create permission sets for specific features
✓ Name clearly: "Data_Cloud_Query_User" not "Special_Access"
✓ Use permission set groups for related permissions
✓ Document what each permission set grants
✓ Set expiration dates for temporary access
✓ Regular audit of assignments
```

**DON'T:**
```
✗ Create user-specific permission sets
✗ Grant overly broad permissions
✗ Duplicate profile permissions in permission sets
✗ Create permission sets without clear purpose
```

### 4. Data Cloud Permissions

**DO:**
```
✓ Use tiered permission sets (Query → Segment → Activate)
✓ Apply field-level security on sensitive fields
✓ Use custom permission sets for DMO access
✓ Grant minimum necessary permissions
✓ Separate read and write permissions
```

**DON'T:**
```
✗ Give everyone "Data Cloud Admin"
✗ Grant access to all DMOs by default
✗ Ignore field-level security on PII
✗ Mix data access with admin permissions
```

---

## TROUBLESHOOTING

### "User can't access Data Cloud"

**Check in this order:**

```
1. LICENSE
   Question: Does user have Data Cloud license?
   Check: Setup → Users → [User] → License
   Fix: Assign Data Cloud license

2. PROFILE/PERMISSION SET
   Question: Does user have "Data Cloud User" permission?
   Check: Profile OR any permission set
   Fix: Assign "Data Cloud User" permission set

3. APP ACCESS
   Question: Can user see Data Cloud app?
   Check: App Manager → Data Cloud → User Profiles
   Fix: Add profile to Data Cloud app visibility

4. OBJECT ACCESS
   Question: Does user have DMO permissions?
   Check: Permission sets → Object Settings
   Fix: Grant specific DMO access
```

---

### "User can query but can't create segments"

```
Diagnosis: Has query permissions but not segmentation

1. Check profile: Does it grant segmentation?
   Usually: NO (profiles are minimal)

2. Check permission sets:
   - Has "Data Cloud Query User"? ✓
   - Has "Data Cloud Segmentation User"? ✗

Fix: Assign "Data Cloud Segmentation User" permission set
```

---

### "User can't see specific field in DMO"

```
Diagnosis: Field-level security issue

1. Check object access:
   - Can user query the DMO? ✓
   - Object permission: Read ✓

2. Check field-level security:
   - Profile: Field access? Check FLS
   - Permission Sets: Any grant field access?

3. Check field security settings:
   Setup → Object Manager → [DMO] → Fields → [Field] → Set Field-Level Security

Fix: Grant field Read permission in profile or permission set
```

---

## SUMMARY: KEY CONCEPTS

### The Hierarchy

```
LICENSE (What you bought)
  ↓
PROFILE (Your baseline role)
  ↓
PERMISSION SETS (Additional feature access)
  ↓
FIELD-LEVEL SECURITY (Specific field restrictions)
  ↓
SHARING RULES (Which records you can see)
  ↓
FINAL ACCESS (Combination of all above)
```

### The Formula

```
Access = License ∩ (Profile ∪ PermissionSet1 ∪ PermissionSet2 ∪ ...) ∩ FLS ∩ Sharing

Where:
∩ = AND (most restrictive wins)
∪ = OR (most permissive wins)

License restricts everything (hard limit)
Profile OR Permission Sets (grant permissions)
FLS restricts fields
Sharing restricts records
```

### Quick Reference

| Need | Solution |
|------|----------|
| Access to Salesforce | Assign License |
| Basic CRM access | Assign Profile |
| Extra feature access | Assign Permission Set |
| Hide sensitive field | Apply Field-Level Security |
| Limit record visibility | Configure Sharing Rules |
| Data Cloud access | License + Data Cloud permission sets |
| Temporary access | Permission Set with expiration |
| Role-based access | Profile |
| Feature-based access | Permission Set |

---

## GLOSSARY

**License** - Purchased right to use Salesforce/Data Cloud, required for login

**Profile** - Baseline permissions assigned to user (exactly 1 per user)

**Permission Set** - Additional permissions beyond profile (0 to many per user)

**Permission Set Group** - Bundle of permission sets assigned together

**Object Permission** - Access level to an object (CRUD, View All, Modify All)

**Field-Level Security (FLS)** - Read/Edit access to specific fields

**System Permission** - Global capabilities (API access, View Setup, etc.)

**Sharing Rules** - Row-level security determining which records user can access

**CRUD** - Create, Read, Update, Delete permissions

**DMO** - Data Model Object (Data Cloud object type)

**DLO** - Data Lake Object (Data Cloud object type)

---

*This guide provides the foundation for understanding Salesforce and Data Cloud security. Use it as a reference when reading official documentation.*

*Last updated: 2025*
