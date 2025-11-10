# Data Cloud Governance: Comprehensive Reference Guide

## Table of Contents
1. [Policy Types](#policy-types)
2. [Tags & Labels](#tags--labels)
3. [Metadata Fields](#metadata-fields)
4. [Governance Objects](#governance-objects)
5. [Configuration Settings](#configuration-settings)
6. [Audit & Monitoring Elements](#audit--monitoring-elements)
7. [System Fields](#system-fields)

---

## POLICY TYPES

### 1. Access Control Policies

**Profile-Based Access Policies**
- `ProfilePermission` - Base permissions assigned via profile
- `ProfileObjectAccess` - Object-level access (CRUD) per profile
- `ProfileFieldAccess` - Field-level access per profile
- `ProfileTabAccess` - UI tab visibility per profile

**Permission Set Policies**
- `PermissionSet` - Named permission bundle
- `PermissionSetAssignment` - User-to-permission set mapping
- `PermissionSetGroup` - Bundled permission sets
- `PermissionSetGroupAssignment` - User-to-group mapping

**Object-Level Security (OLS) Policies**
- `ObjectPermissions` - Create, Read, Update, Delete, View All, Modify All
- `DataModelObjectAccess` - DMO-specific access rules
- `DataLakeObjectAccess` - DLO-specific access rules
- `StreamingObjectAccess` - Real-time data stream access

**Field-Level Security (FLS) Policies**
- `FieldPermissions` - Read/Edit access per field
- `SensitiveFieldAccess` - Special handling for PII fields
- `FieldEncryptionPolicy` - Field-level encryption rules
- `MaskedFieldPolicy` - Dynamic field masking rules

**Row-Level Security (RLS) Policies**
- `SharingRule` - Record sharing between users/groups
- `SharingSet` - Data Cloud-specific row filtering
- `OwnershipBasedSharing` - Share based on record ownership
- `CriteriaBasedSharing` - Share based on field values
- `ManualSharing` - One-off record sharing

**Data Space Policies**
- `DataSpaceDefinition` - Logical data partition
- `DataSpaceAccess` - User access to specific data space
- `DataSpaceIsolation` - Cross-space access rules
- `DataSpaceOwnership` - Administrative control

---

### 2. Privacy & Consent Policies

**Consent Management Policies**
- `ConsentDefinition` - Purpose for data collection
- `ConsentRecord` - Individual's consent status
- `ConsentCategory` - Grouping of related consents
- `ConsentExpiration` - Time-based consent validity
- `ConsentCapture` - How consent was obtained
- `ConsentWithdrawal` - Opt-out processing rules

**Data Purpose Policies**
- `DataPurposeDefinition` - Why data was collected
- `PurposeUsageRule` - Allowed operations per purpose
- `PurposeLegalBasis` - GDPR legal basis (consent, contract, etc.)
- `PurposeEnforcement` - Where purpose is enforced
- `PurposeHierarchy` - Parent-child purpose relationships

**Privacy Policies**
- `DataSubjectRights` - GDPR/CCPA rights configuration
- `RightToAccess` - Data portability settings
- `RightToErasure` - Deletion request handling
- `RightToRectification` - Data correction processes
- `RightToRestriction` - Processing limitation rules
- `RightToObject` - Objection handling

**Preference Management Policies**
- `ChannelPreference` - Email, SMS, phone opt-in/out
- `FrequencyPreference` - Communication frequency limits
- `ContentPreference` - Topic/category interests
- `LanguagePreference` - Communication language
- `TimingPreference` - Preferred contact times

**Compliance Policies**
- `GDPRComplianceRule` - EU-specific rules
- `CCPAComplianceRule` - California-specific rules
- `HIPAAComplianceRule` - Healthcare data rules
- `DataResidencyPolicy` - Geographic storage requirements
- `CrossBorderTransferRule` - International data movement

---

### 3. Data Quality Policies

**Validation Policies**
- `FieldValidationRule` - Format/pattern enforcement
- `DataTypeValidation` - Type checking (string, number, date)
- `RangeValidation` - Min/max value checks
- `ListValidation` - Allowed values (enum)
- `RegexValidation` - Pattern matching
- `CustomValidation` - Complex business rules

**Completeness Policies**
- `RequiredFieldRule` - Mandatory fields
- `CompletenessThreshold` - Minimum % of fields populated
- `NullValuePolicy` - How to handle nulls
- `DefaultValueRule` - Fallback values

**Uniqueness Policies**
- `UniqueConstraint` - Field uniqueness within object
- `DuplicateDetectionRule` - Cross-record duplicate rules
- `CanonicalValueRule` - Standardization rules

**Consistency Policies**
- `CrossFieldValidation` - Field relationship rules
- `ReferentialIntegrity` - Foreign key validation
- `FormatStandardization` - Consistent formatting
- `TimeSeriesConsistency` - Temporal data rules

**Data Profiling Policies**
- `ProfilingSchedule` - When to analyze data
- `ProfilingScope` - What data to analyze
- `ProfilingMetrics` - What to measure
- `ProfilingThresholds` - Alert thresholds

---

### 4. Data Retention & Deletion Policies

**Retention Policies**
- `RetentionRule` - How long to keep data
- `RetentionSchedule` - When retention periods start
- `RetentionException` - Legal hold overrides
- `ArchivalPolicy` - Move to cold storage rules

**Deletion Policies**
- `AutoDeletionRule` - Scheduled deletion
- `CascadingDeleteRule` - Related record deletion
- `SoftDeletePolicy` - Mark as deleted vs hard delete
- `DeletionApprovalRule` - Approval workflow for deletion

**Anonymization Policies**
- `AnonymizationRule` - When to anonymize
- `AnonymizationMethod` - How to anonymize (hash, mask, aggregate)
- `PseudonymizationRule` - Replace with pseudonym
- `DeIdentificationRule` - Remove identifying data

---

### 5. Data Security Policies

**Encryption Policies**
- `EncryptionAtRest` - Storage encryption settings
- `EncryptionInTransit` - Network encryption (TLS)
- `FieldLevelEncryption` - Specific field encryption
- `KeyManagementPolicy` - Encryption key rotation
- `BringYourOwnKey` - Customer-managed keys

**Data Masking Policies**
- `DynamicMasking` - Real-time data masking
- `StaticMasking` - Masked copy for non-prod
- `MaskingFormat` - How to mask (partial, full, random)
- `MaskingScope` - When masking applies

**Classification Policies**
- `DataClassification` - Sensitivity levels
- `AutoClassificationRule` - ML-based classification
- `ClassificationTags` - Labels for classified data
- `ClassificationPropagation` - Inherit classification

**Security Monitoring Policies**
- `AnomalyDetection` - Unusual access patterns
- `ThreatDetection` - Security threat rules
- `AccessMonitoring` - Track data access
- `ExfiltrationPrevention` - Prevent bulk exports

---

### 6. Identity Resolution Policies

**Matching Policies**
- `MatchRule` - How to match records
- `MatchingAlgorithm` - Exact, fuzzy, ML-based
- `MatchConfidenceThreshold` - Minimum match score
- `MatchingFieldWeights` - Field importance

**Reconciliation Policies**
- `ReconciliationRule` - Conflict resolution
- `SourcePriority` - Which source wins
- `FieldMergeRule` - How to combine values
- `TimestampBasedRule` - Most recent wins

**Identity Policies**
- `IndividualIDGeneration` - Unified ID creation
- `ContactPointType` - Email, phone, address rules
- `ContactPointMatching` - How to match contact points
- `IndividualMergePolicy` - When to merge identities

---

### 7. Segmentation & Activation Policies

**Segmentation Policies**
- `SegmentAccessControl` - Who can create segments
- `SegmentSizeLimit` - Max segment size
- `SegmentRefreshPolicy` - Update frequency
- `SegmentRetention` - How long to keep segments

**Activation Policies**
- `ActivationDestinationRule` - Allowed destinations
- `ActivationApprovalRule` - Approval workflow
- `ActivationFrequencyLimit` - Max activations per period
- `ActivationConsentCheck` - Require consent validation

**Data Minimization Policies**
- `MinimalFieldSet` - Only send required fields
- `DataFilterRule` - Exclude sensitive data
- `AggregationRule` - Send aggregated vs individual data

**Destination Control Policies**
- `AllowedDestinations` - Whitelist of approved systems
- `BlockedDestinations` - Blacklist of prohibited systems
- `DestinationEncryption` - Encryption requirements
- `DestinationAudit` - Logging requirements

---

### 8. Audit & Monitoring Policies

**Audit Logging Policies**
- `AuditLogRetention` - How long to keep logs
- `AuditEventType` - What to log
- `AuditDetailLevel` - Verbosity of logs
- `AuditLogAccess` - Who can view logs

**Monitoring Policies**
- `RealTimeMonitoring` - Live monitoring rules
- `AlertRule` - When to send alerts
- `AlertThreshold` - Trigger conditions
- `AlertRecipient` - Who gets notified

**Compliance Reporting Policies**
- `ReportSchedule` - When to generate reports
- `ReportContent` - What to include
- `ReportDistribution` - Who receives reports
- `ReportRetention` - How long to keep reports

---

## TAGS & LABELS

### 1. Data Classification Tags

**Sensitivity Tags**
- `Public` - No restrictions
- `Internal` - Employees only
- `Confidential` - Need-to-know basis
- `Restricted` - Highest security
- `HighlySensitive` - Extra controls required

**Content Type Tags**
- `PII` - Personally Identifiable Information
- `SPI` - Sensitive Personal Information
- `PHI` - Protected Health Information
- `PCI` - Payment Card Information
- `Financial` - Financial data
- `Proprietary` - Trade secrets

**Regulatory Tags**
- `GDPR` - Subject to GDPR
- `CCPA` - Subject to CCPA
- `HIPAA` - Subject to HIPAA
- `SOX` - Sarbanes-Oxley compliance
- `PCI-DSS` - Payment card compliance

---

### 2. Purpose Tags

**Business Purpose Tags**
- `Marketing` - Marketing communications
- `Sales` - Sales operations
- `Service` - Customer support
- `Analytics` - Business intelligence
- `Operations` - Operational processes
- `Research` - Research and development

**Processing Purpose Tags**
- `Transactional` - Order/transaction processing
- `Personalization` - Customized experience
- `Profiling` - Behavioral analysis
- `Targeting` - Advertising targeting
- `Sharing` - Third-party sharing
- `AuditTrail` - Compliance logging

**Legal Basis Tags (GDPR)**
- `Consent` - Article 6(1)(a)
- `Contract` - Article 6(1)(b)
- `LegalObligation` - Article 6(1)(c)
- `VitalInterest` - Article 6(1)(d)
- `PublicTask` - Article 6(1)(e)
- `LegitimateInterest` - Article 6(1)(f)

---

### 3. Data Lineage Tags

**Source Tags**
- `SourceSystem` - Origin system name
- `SourceType` - CRM, ERP, Web, Mobile, etc.
- `IngestionMethod` - Batch, streaming, API
- `IngestionDate` - When data arrived
- `DataOwner` - Responsible team/person

**Transformation Tags**
- `TransformationType` - ETL, calculation, enrichment
- `TransformationDate` - When transformed
- `TransformationLogic` - Business rule applied
- `DerivedField` - Calculated from other fields

**Quality Tags**
- `DataQualityScore` - Overall quality rating
- `CompletenessScore` - % fields populated
- `AccuracyScore` - Validation pass rate
- `TimelinessScore` - Data freshness
- `Verified` - Human-verified data
- `Inferred` - ML-inferred data

---

### 4. Lifecycle Tags

**Status Tags**
- `Active` - Currently in use
- `Inactive` - Not currently used
- `Archived` - Moved to cold storage
- `Deprecated` - Marked for removal
- `Deleted` - Soft deleted
- `Quarantined` - Quality issues

**Temporal Tags**
- `CreatedDate` - When record created
- `LastModifiedDate` - Last update
- `LastAccessedDate` - Last queried
- `ExpirationDate` - When to delete
- `ArchiveDate` - When archived

---

### 5. Privacy Tags

**Consent Tags**
- `ConsentStatus` - Opted in, out, pending
- `ConsentDate` - When consent given
- `ConsentChannel` - Web, mobile, email
- `ConsentVersion` - Policy version
- `ConsentExpiry` - When consent expires

**Processing Tags**
- `ProcessingRestriction` - Limit use
- `ObjectionReceived` - Customer objected
- `DoNotTrack` - Honor DNT header
- `ChildData` - Under age of consent
- `SpecialCategory` - GDPR special categories

---

### 6. Governance Tags

**Ownership Tags**
- `DataSteward` - Responsible person
- `BusinessOwner` - Business contact
- `TechnicalOwner` - Technical contact
- `CostCenter` - Billing allocation

**Usage Tags**
- `UsageFrequency` - How often accessed
- `CriticalityLevel` - Business importance
- `UsageContext` - Production, dev, test
- `SharedWith` - External parties

---

## METADATA FIELDS

### 1. Object-Level Metadata

**DMO (Data Model Object) Metadata**
```
- ObjectApiName: string
- ObjectLabel: string
- ObjectDescription: string
- ObjectType: DMO | DLO | External
- RecordCount: number
- StorageSize: bytes
- CreatedBy: userId
- CreatedDate: timestamp
- ModifiedBy: userId
- ModifiedDate: timestamp
- IsActive: boolean
- IsIndexed: boolean
- PartitionStrategy: field name
- RetentionPeriod: days
```

**DLO (Data Lake Object) Metadata**
```
- ObjectApiName: string
- SourceSystem: string
- SourceLocation: URI
- FileFormat: CSV | Parquet | JSON
- Schema: JSON schema
- CompressionType: gzip | snappy
- PartitionKeys: [field names]
- UpdateFrequency: cron expression
- LastRefreshDate: timestamp
- NextRefreshDate: timestamp
```

---

### 2. Field-Level Metadata

**Field Definition Metadata**
```
- FieldApiName: string
- FieldLabel: string
- FieldDescription: string
- DataType: string | number | date | boolean
- Length: number
- Precision: number
- Scale: number
- IsRequired: boolean
- IsUnique: boolean
- IsIndexed: boolean
- DefaultValue: any
- PicklistValues: [values]
```

**Field Governance Metadata**
```
- SensitivityLevel: public | internal | confidential | restricted
- ContainsPII: boolean
- ContainsPHI: boolean
- ContainsPCI: boolean
- EncryptionEnabled: boolean
- MaskingRule: ruleName
- DataClassification: classification
- RetentionPeriod: days
- Purpose: [purposes]
- LegalBasis: basis type
```

**Field Quality Metadata**
```
- NullPercentage: number
- UniqueValueCount: number
- MinValue: any
- MaxValue: any
- AverageValue: number
- StandardDeviation: number
- OutlierCount: number
- ValidationRule: ruleName
- QualityScore: number (0-100)
- LastProfiledDate: timestamp
```

---

### 3. Individual (Customer) Metadata

**Identity Metadata**
```
- IndividualId: GUID
- FirstSeenDate: timestamp
- LastSeenDate: timestamp
- SourceRecordCount: number
- ConfidenceScore: number (0-1)
- IdentityResolutionDate: timestamp
- MatchMethod: exact | fuzzy | ML
- IsGoldenRecord: boolean
```

**Consent Metadata**
```
- ConsentRecords: [consent objects]
- EmailConsent: opted-in | opted-out
- SMSConsent: opted-in | opted-out
- PhoneConsent: opted-in | opted-out
- ThirdPartyConsent: opted-in | opted-out
- ConsentLastUpdated: timestamp
- ConsentSource: channel
```

**Privacy Metadata**
```
- DataSubjectRightsRequests: [DSAR objects]
- RightToAccessRequested: boolean
- RightToErasureRequested: boolean
- ProcessingRestricted: boolean
- DoNotProcess: boolean
- MinorStatus: boolean
- JurisdictionCode: ISO country code
```

---

### 4. Segment Metadata

**Segment Definition Metadata**
```
- SegmentId: GUID
- SegmentName: string
- SegmentDescription: string
- SegmentType: static | dynamic
- CreatedBy: userId
- CreatedDate: timestamp
- MemberCount: number
- RefreshFrequency: cron
- LastRefreshDate: timestamp
- IsActive: boolean
```

**Segment Governance Metadata**
```
- RequiredConsent: [consent purposes]
- AllowedActivations: [destination types]
- ApprovalRequired: boolean
- ApprovedBy: userId
- ApprovalDate: timestamp
- DataPurpose: purpose
- RetentionPeriod: days
```

---

### 5. Activation Metadata

**Activation Definition Metadata**
```
- ActivationId: GUID
- ActivationName: string
- DestinationType: email | ad platform | etc
- DestinationName: string
- SegmentId: GUID
- Status: active | paused | completed
- Schedule: cron expression
- CreatedDate: timestamp
- LastRunDate: timestamp
```

**Activation Execution Metadata**
```
- RecordsSent: number
- RecordsFiltered: number (consent)
- RecordsFailed: number
- BytesTransferred: bytes
- ExecutionTime: milliseconds
- ConsentCheckPassed: boolean
- ErrorLog: [errors]
```

---

### 6. Audit Metadata

**Audit Event Metadata**
```
- EventId: GUID
- EventType: access | modify | delete | export
- EventTimestamp: timestamp
- UserId: userId
- UserRole: role
- ObjectAccessed: object name
- FieldsAccessed: [field names]
- RecordCount: number
- IPAddress: IP
- DeviceInfo: user agent
- Location: geo coordinates
- Action: what was done
- Result: success | failure
- Reason: error message if failed
```

---

## GOVERNANCE OBJECTS

### 1. Core Governance Objects

**Individual**
- Unified customer profile
- Identity resolution result
- Consent records attached
- Privacy rights requests

**Consent Object**
- Purpose of consent
- Status (opted in/out)
- Capture date/time
- Capture method
- Expiration date

**Data Purpose Object**
- Purpose name
- Legal basis
- Allowed operations
- Retention period
- Enforcement rules

**Data Space Object**
- Logical partition name
- Included objects
- Access control list
- Isolation rules

---

### 2. Identity Resolution Objects

**Match Rule**
- Matching algorithm
- Field weights
- Confidence threshold
- Fuzzy matching config

**Reconciliation Rule**
- Source priority order
- Field merge strategy
- Conflict resolution logic

**Individual Source**
- Source system
- Source record ID
- Contribution to golden record

---

### 3. Data Quality Objects

**Validation Rule**
- Rule name
- Validation logic
- Error message
- Severity level

**Data Quality Dashboard**
- Quality metrics
- Trend analysis
- Issue summary
- Recommendations

**Data Profile**
- Statistical summary
- Distribution charts
- Anomaly detection
- Quality scores

---

### 4. Security Objects

**Permission Set**
- Permission bundle
- Object permissions
- Field permissions
- User assignments

**Sharing Rule**
- Criteria
- Share with
- Access level
- Rule priority

**Encryption Policy**
- Fields to encrypt
- Encryption method
- Key management
- Decryption permissions

---

### 5. Activation Objects

**Activation Target**
- Destination system
- Connection details
- Field mappings
- Credentials

**Activation Schedule**
- Frequency
- Time of day
- Batch size
- Error handling

**Activation Log**
- Execution history
- Success/failure status
- Record counts
- Error details

---

## CONFIGURATION SETTINGS

### 1. Global Settings

**Organization Settings**
```
- OrgId: unique identifier
- OrgName: organization name
- DefaultDataSpace: default space
- DefaultLocale: en_US
- DefaultTimezone: UTC
- DataResidency: region (US, EU, APAC)
```

**Security Settings**
```
- SessionTimeout: minutes
- PasswordPolicy: rules
- MFARequired: boolean
- IPWhitelist: [IP ranges]
- EncryptionEnabled: boolean
- AuditLevel: minimal | standard | verbose
```

**Privacy Settings**
```
- GDPREnabled: boolean
- CCPAEnabled: boolean
- DefaultConsentMode: opt-in | opt-out
- ConsentExpirationDays: days
- DSARResponseTime: days
- PrivacyPolicyURL: URL
```

---

### 2. Data Management Settings

**Ingestion Settings**
```
- BatchSize: records
- StreamingEnabled: boolean
- DuplicateHandling: update | skip | error
- ValidationOnIngestion: boolean
- AutoClassification: boolean
```

**Identity Resolution Settings**
```
- AutoMatchingEnabled: boolean
- MatchingThreshold: 0-1
- ReconciliationMode: automatic | manual
- IndividualIDFormat: format string
- MaxSourcesPerIndividual: number
```

**Retention Settings**
```
- DefaultRetentionPeriod: days
- AutoArchivingEnabled: boolean
- SoftDeleteEnabled: boolean
- PurgeSchedule: cron
```

---

### 3. Query & Segmentation Settings

**Query Settings**
```
- MaxQueryResultSize: rows
- QueryTimeout: seconds
- CacheEnabled: boolean
- CacheTTL: seconds
- ParallelQueriesEnabled: boolean
```

**Segmentation Settings**
```
- MaxSegmentSize: members
- RefreshFrequency: cron
- StaticSegmentEnabled: boolean
- DynamicSegmentEnabled: boolean
- NestedSegmentDepth: number
```

---

### 4. Activation Settings

**Destination Settings**
```
- AllowedDestinationTypes: [types]
- ApprovalRequired: boolean
- MaxActivationSize: records
- RetryAttempts: number
- ErrorNotification: email
```

**Consent Enforcement Settings**
```
- EnforceConsentOnActivation: boolean
- RequireExplicitConsent: boolean
- HonorGlobalOptOut: boolean
- ConsentCheckLevel: strict | permissive
```

---

## AUDIT & MONITORING ELEMENTS

### 1. Audit Log Fields

**Standard Audit Fields**
```
- AuditId: GUID
- Timestamp: ISO 8601
- EventType: string
- EventCategory: access | change | admin
- Severity: info | warning | error | critical
- UserId: GUID
- Username: string
- UserRole: string
- SessionId: GUID
- IPAddress: IPv4/IPv6
- UserAgent: string
- Location: geo
```

**Data Access Audit Fields**
```
- ObjectType: DMO | DLO
- ObjectName: string
- Action: query | export | view
- RecordCount: number
- FieldsAccessed: [field names]
- QueryText: SQL
- FilterCriteria: JSON
- ResultSize: bytes
```

**Data Modification Audit Fields**
```
- Action: create | update | delete
- RecordId: GUID
- FieldsChanged: [field names]
- OldValues: JSON
- NewValues: JSON
- ChangeReason: string
- ApprovedBy: userId (if required)
```

---

### 2. Monitoring Metrics

**System Metrics**
```
- CPUUtilization: percentage
- MemoryUsage: bytes
- StorageUsage: bytes
- QueryThroughput: queries/second
- IngestionRate: records/second
- ActivationRate: records/second
```

**Data Quality Metrics**
```
- OverallQualityScore: 0-100
- CompletenessScore: 0-100
- AccuracyScore: 0-100
- TimelinessScore: 0-100
- ConsistencyScore: 0-100
- DuplicateRate: percentage
- ValidationFailureRate: percentage
```

**Governance Metrics**
```
- ConsentOptInRate: percentage
- ConsentOptOutRate: percentage
- DSARCount: number
- DSARCompletionTime: hours
- PolicyViolationCount: number
- UnauthorizedAccessAttempts: number
- DataBreachIncidents: number
```

**Usage Metrics**
```
- ActiveUsers: number
- QueryCount: number
- SegmentCount: number
- ActivationCount: number
- StorageGrowthRate: bytes/day
- CreditConsumption: credits
```

---

### 3. Alert Definitions

**Security Alerts**
```
- UnauthorizedAccessAttempt
- AnomalousAccessPattern
- BulkExportDetected
- PrivilegeEscalation
- EncryptionKeyCompromise
- DataExfiltrationSuspected
```

**Quality Alerts**
```
- QualityScoreBelowThreshold
- DuplicateRateIncreased
- ValidationFailureSpike
- DataFreshnessIssue
- SchemaViolation
- IncompleteDataDetected
```

**Compliance Alerts**
```
- DSARDeadlineApproaching
- ConsentExpirationPending
- RetentionPolicyViolation
- UnauthorizedPurposeUsage
- CrossBorderTransferBlocked
- PolicyEnforcementFailure
```

**Operational Alerts**
```
- IngestionFailure
- IdentityResolutionError
- ActivationFailure
- StorageQuotaExceeded
- CreditLimitApproaching
- SystemPerformanceDegraded
```

---

## SYSTEM FIELDS

### 1. Standard System Fields

**All Objects**
```
- Id: GUID (primary key)
- CreatedById: userId
- CreatedDate: timestamp
- LastModifiedById: userId
- LastModifiedDate: timestamp
- SystemModstamp: timestamp
- IsDeleted: boolean
```

### 2. Data Cloud-Specific Fields

**Individual Object**
```
- IndividualId: GUID
- DataSourcesCount: number
- ConsentStatusSummary: JSON
- LastEngagementDate: timestamp
- LifecycleStage: string
- DataCompletenessScore: number
```

**DMO/DLO Objects**
```
- DataSpaceId: GUID
- SourceSystemId: string
- IngestionTimestamp: timestamp
- DataQualityScore: number
- IsGoldenRecord: boolean
- ParentRecordId: GUID (for hierarchies)
```

**Calculated Insights**
```
- CalculationDate: timestamp
- CalculationMethod: formula | ML model
- ConfidenceScore: 0-1
- RefreshSchedule: cron
- DependentFields: [field names]
```

---

## USAGE PATTERNS

### Common Governance Patterns

**Pattern 1: PII Protection**
```
Tags: [PII, Confidential, GDPR]
Policies: [FieldLevelEncryption, MaskingPolicy, AccessControl]
Metadata: {containsPII: true, sensitivityLevel: "restricted"}
```

**Pattern 2: Consent-Based Marketing**
```
Tags: [Marketing, EmailMarketing, RequiresConsent]
Policies: [ConsentEnforcement, PurposeValidation, ActivationControl]
Metadata: {purpose: "EmailMarketing", legalBasis: "Consent"}
```

**Pattern 3: Data Quality Monitoring**
```
Tags: [QualityMonitored, CriticalData]
Policies: [ValidationRule, CompletenessCheck, DuplicateDetection]
Metadata: {qualityScore: 95, lastProfiled: "2024-01-15"}
```

**Pattern 4: Compliance Reporting**
```
Tags: [GDPR, AuditRequired, HighRisk]
Policies: [AuditLogging, RetentionEnforcement, AccessMonitoring]
Metadata: {regulatoryRequirement: "GDPR", retentionPeriod: 2555}
```

---

## QUICK REFERENCE CHEAT SHEET

### Most Important Policies
1. ConsentEnforcement
2. DataPurposeValidation
3. FieldLevelSecurity
4. RetentionPolicy
5. AuditLogging

### Most Important Tags
1. PII / SPI / PHI
2. ConsentStatus
3. DataPurpose
4. SensitivityLevel
5. RegulatoryTag (GDPR/CCPA)

### Most Important Metadata
1. IndividualId
2. ConsentRecords
3. DataClassification
4. RetentionPeriod
5. LastModifiedDate

### Most Important Audit Fields
1. EventType
2. UserId
3. ObjectAccessed
4. Timestamp
5. Action + Result

---

*This reference guide covers the comprehensive set of policies, tags, and metadata available for Data Cloud governance. Use this as a foundation to build your governance framework.*

*Last updated: 2025*
