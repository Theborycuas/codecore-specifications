# 06-audit-management/entities.md

````md id="w9x3vp"
# Audit Management Entities

## 1. Introduction

This document defines the entities of the Audit Management module.

Entities represent domain objects that:

- Possess identity
- Maintain lifecycle continuity
- Preserve immutable evidence
- Support distributed traceability
- Enable compliance auditing
- Facilitate forensic reconstruction

The entities are designed following:

- Domain-Driven Design (DDD)
- Immutable evidence principles
- Event-driven architecture
- Multi-tenant SaaS isolation
- Enterprise compliance standards

---

# 2. Entity Overview

| Entity | Purpose |
|---|---|
| AuditRecord | Core immutable audit evidence |
| SecurityAuditRecord | Security-related audit evidence |
| ComplianceAuditRecord | Regulatory traceability evidence |
| SensitiveAccessRecord | Sensitive resource access tracking |
| CorrelationTrace | Distributed operation trace |
| TraceSegment | Individual distributed trace step |
| AuditRetentionPolicy | Audit lifecycle rules |
| AuditExport | Compliance export lifecycle |
| AuditIntegrityProof | Tamper evidence validation |
| AuditMetadata | Extended operational metadata |
| ThreatIndicator | Security risk indicators |
| AuditAttachment | Supplemental evidence references |
| LegalHold | Retention override protection |
| AuditArchiveReference | Archived evidence locator |

---

# 3. AuditRecord Entity

## Purpose

Represents the foundational immutable audit event.

---

## Identity

```text id="p4n8wr"
auditRecordId
````

---

## Attributes

| Attribute     | Type    | Description             |
| ------------- | ------- | ----------------------- |
| auditRecordId | UUID    | Unique audit identifier |
| tenantId      | String  | Tenant context          |
| actorId       | UUID    | User/service actor      |
| action        | String  | Executed action         |
| resourceType  | String  | Target resource         |
| resourceId    | String  | Resource identifier     |
| result        | Enum    | SUCCESS/FAILURE         |
| occurredAt    | Instant | Event timestamp         |
| correlationId | String  | Distributed trace       |
| metadata      | Object  | Additional context      |

---

## Behaviors

| Behavior            | Description                 |
| ------------------- | --------------------------- |
| persist()           | Stores immutable evidence   |
| validateIntegrity() | Validates audit consistency |
| enrichMetadata()    | Adds operational context    |

---

## Business Rules

* Immutable after persistence
* Tenant context mandatory
* Correlation ID preserved
* Timestamp immutable

---

# 4. SecurityAuditRecord Entity

## Purpose

Represents security-focused audit evidence.

---

## Identity

```text id="x7m2vt"
securityAuditRecordId
```

---

## Attributes

| Attribute             | Type   | Description              |
| --------------------- | ------ | ------------------------ |
| securityAuditRecordId | UUID   | Security audit ID        |
| severity              | Enum   | LOW/MEDIUM/HIGH/CRITICAL |
| threatType            | String | Security event category  |
| ipAddress             | String | Origin IP                |
| deviceId              | String | Device identifier        |
| detectionSource       | String | Detection mechanism      |
| evidence              | Object | Supporting evidence      |

---

## Behaviors

| Behavior               | Description             |
| ---------------------- | ----------------------- |
| classifySeverity()     | Assigns risk severity   |
| appendThreatEvidence() | Adds indicators         |
| escalate()             | Marks critical incident |

---

## Example Events

```text id="g5v9xp"
- MFA failure
- Token replay
- Unauthorized access
- Cross-tenant attempt
```

---

## Business Rules

* Severity mandatory
* Immutable evidence required
* Threat indicators preserved

---

# 5. ComplianceAuditRecord Entity

## Purpose

Represents compliance-related evidence.

---

## Identity

```text id="u3x8wr"
complianceAuditRecordId
```

---

## Attributes

| Attribute               | Type   | Description           |
| ----------------------- | ------ | --------------------- |
| complianceAuditRecordId | UUID   | Compliance audit ID   |
| regulationType          | String | HIPAA/GDPR/etc        |
| complianceCategory      | String | Privacy/access/export |
| legalBasis              | String | Compliance rationale  |
| retentionPolicyId       | UUID   | Retention linkage     |

---

## Behaviors

| Behavior                  | Description             |
| ------------------------- | ----------------------- |
| validateComplianceScope() | Validates context       |
| enforceRetention()        | Applies retention rules |

---

## Business Rules

* Regulatory context required
* Legal traceability mandatory
* Retention linkage mandatory

---

# 6. SensitiveAccessRecord Entity

## Purpose

Tracks access to sensitive resources.

Critical for medical systems.

---

## Identity

```text id="k8v4wp"
sensitiveAccessRecordId
```

---

## Attributes

| Attribute               | Type    | Description        |
| ----------------------- | ------- | ------------------ |
| sensitiveAccessRecordId | UUID    | Access record ID   |
| actorId                 | UUID    | Accessing user     |
| accessedResource        | String  | Sensitive resource |
| accessReason            | String  | Declared reason    |
| accessType              | Enum    | VIEW/EXPORT/MODIFY |
| occurredAt              | Instant | Access timestamp   |

---

## Behaviors

| Behavior                | Description       |
| ----------------------- | ----------------- |
| validateAccessContext() | Verifies access   |
| registerAccess()        | Persists evidence |

---

## Example Sensitive Resources

```text id="f2m7vr"
- Medical records
- Psychological evaluations
- Consent forms
```

---

## Business Rules

* Actor identity mandatory
* Resource identity mandatory
* Immutable evidence required

---

# 7. CorrelationTrace Entity

## Purpose

Represents distributed operational tracing.

---

## Identity

```text id="r1x6vt"
correlationTraceId
```

---

## Attributes

| Attribute          | Type    | Description           |
| ------------------ | ------- | --------------------- |
| correlationTraceId | UUID    | Trace identifier      |
| correlationId      | String  | Distributed trace key |
| initiatedAt        | Instant | Trace start           |
| completedAt        | Instant | Trace end             |
| status             | Enum    | ACTIVE/COMPLETED      |

---

## Behaviors

| Behavior              | Description           |
| --------------------- | --------------------- |
| appendSegment()       | Adds service step     |
| reconstructTimeline() | Builds operation flow |

---

# 8. TraceSegment Entity

## Purpose

Represents individual distributed service hops.

---

## Identity

```text id="d6n3xp"
traceSegmentId
```

---

## Attributes

| Attribute      | Type    | Description        |
| -------------- | ------- | ------------------ |
| traceSegmentId | UUID    | Segment ID         |
| serviceName    | String  | Origin service     |
| operation      | String  | Executed operation |
| startedAt      | Instant | Start timestamp    |
| completedAt    | Instant | End timestamp      |
| result         | Enum    | SUCCESS/FAILURE    |

---

## Behaviors

| Behavior            | Description       |
| ------------------- | ----------------- |
| complete()          | Finalizes segment |
| calculateDuration() | Computes latency  |

---

# 9. AuditRetentionPolicy Entity

## Purpose

Defines retention lifecycle rules.

---

## Identity

```text id="y9v2wr"
retentionPolicyId
```

---

## Attributes

| Attribute          | Type     | Description           |
| ------------------ | -------- | --------------------- |
| retentionPolicyId  | UUID     | Policy identifier     |
| policyName         | String   | Policy name           |
| retentionPeriod    | Duration | Retention duration    |
| archiveRequired    | Boolean  | Archive requirement   |
| legalHoldSupported | Boolean  | Legal hold capability |

---

## Behaviors

| Behavior             | Description       |
| -------------------- | ----------------- |
| applyRetention()     | Applies lifecycle |
| validateExpiration() | Checks expiration |

---

## Business Rules

* Legal hold overrides expiration
* Retention immutable after activation

---

# 10. AuditExport Entity

## Purpose

Represents audit export operations.

---

## Identity

```text id="v4k8xt"
auditExportId
```

---

## Attributes

| Attribute     | Type    | Description       |
| ------------- | ------- | ----------------- |
| auditExportId | UUID    | Export ID         |
| requestedBy   | UUID    | Requesting actor  |
| exportFormat  | Enum    | CSV/JSON/PDF      |
| exportedAt    | Instant | Export timestamp  |
| exportReason  | String  | Export purpose    |
| exportStatus  | Enum    | PENDING/COMPLETED |

---

## Behaviors

| Behavior                | Description          |
| ----------------------- | -------------------- |
| generateExport()        | Produces export      |
| validateAuthorization() | Verifies permissions |

---

## Business Rules

* Export authorization mandatory
* Export evidence retained

---

# 11. AuditIntegrityProof Entity

## Purpose

Represents tamper evidence validation.

---

## Identity

```text id="m7x1vp"
integrityProofId
```

---

## Attributes

| Attribute        | Type    | Description          |
| ---------------- | ------- | -------------------- |
| integrityProofId | UUID    | Proof identifier     |
| auditRecordId    | UUID    | Protected audit      |
| hashValue        | String  | Integrity hash       |
| generatedAt      | Instant | Generation timestamp |
| algorithm        | String  | Hash algorithm       |

---

## Behaviors

| Behavior          | Description              |
| ----------------- | ------------------------ |
| verifyIntegrity() | Validates hash           |
| generateProof()   | Produces integrity proof |

---

## Business Rules

* Hash immutable
* Tampering detectable
* Verification reproducible

---

# 12. AuditMetadata Entity

## Purpose

Represents extended operational metadata.

---

## Identity

```text id="q5v9wr"
metadataId
```

---

## Attributes

| Attribute   | Type   | Description         |
| ----------- | ------ | ------------------- |
| metadataId  | UUID   | Metadata identifier |
| userAgent   | String | Client metadata     |
| ipAddress   | String | Network origin      |
| geoLocation | String | Optional location   |
| serviceName | String | Origin service      |

---

## Behaviors

| Behavior   | Description              |
| ---------- | ------------------------ |
| sanitize() | Removes unsafe metadata  |
| enrich()   | Adds operational details |

---

# 13. ThreatIndicator Entity

## Purpose

Represents security risk indicators.

---

## Identity

```text id="h8n4xt"
threatIndicatorId
```

---

## Attributes

| Attribute         | Type    | Description         |
| ----------------- | ------- | ------------------- |
| threatIndicatorId | UUID    | Threat identifier   |
| indicatorType     | String  | Threat category     |
| severity          | Enum    | LOW/HIGH/etc        |
| detectedAt        | Instant | Detection timestamp |
| evidence          | Object  | Supporting data     |

---

## Behaviors

| Behavior           | Description     |
| ------------------ | --------------- |
| escalate()         | Raises severity |
| correlateThreats() | Links incidents |

---

# 14. AuditAttachment Entity

## Purpose

Represents supplemental audit evidence.

---

## Identity

```text id="n2v7xp"
attachmentId
```

---

## Attributes

| Attribute        | Type   | Description           |
| ---------------- | ------ | --------------------- |
| attachmentId     | UUID   | Attachment identifier |
| auditRecordId    | UUID   | Linked audit          |
| attachmentType   | String | Evidence category     |
| storageReference | String | Storage location      |

---

## Example Attachments

```text id="x6m3wr"
- Exported reports
- Signed evidence
- Investigation artifacts
```

---

# 15. LegalHold Entity

## Purpose

Represents regulatory preservation holds.

---

## Identity

```text id="g1x8vt"
legalHoldId
```

---

## Attributes

| Attribute   | Type    | Description         |
| ----------- | ------- | ------------------- |
| legalHoldId | UUID    | Hold identifier     |
| holdReason  | String  | Legal rationale     |
| appliedAt   | Instant | Hold timestamp      |
| expiresAt   | Instant | Optional expiration |

---

## Behaviors

| Behavior      | Description    |
| ------------- | -------------- |
| applyHold()   | Activates hold |
| releaseHold() | Removes hold   |

---

## Business Rules

* Legal hold overrides deletion
* Hold actions auditable

---

# 16. AuditArchiveReference Entity

## Purpose

Represents archived audit storage references.

---

## Identity

```text id="p9m4wr"
archiveReferenceId
```

---

## Attributes

| Attribute          | Type    | Description         |
| ------------------ | ------- | ------------------- |
| archiveReferenceId | UUID    | Archive identifier  |
| archiveLocation    | String  | Storage reference   |
| archivedAt         | Instant | Archive timestamp   |
| retrievalStatus    | Enum    | AVAILABLE/RESTORING |

---

## Behaviors

| Behavior  | Description             |
| --------- | ----------------------- |
| archive() | Moves evidence          |
| restore() | Retrieves archived data |

---

# 17. Entity Relationships

```text id="u7v2xp"
AuditRecord
    ├── contains -> AuditMetadata
    ├── linked by -> CorrelationTrace
    ├── protected by -> AuditIntegrityProof
    └── extended by -> AuditAttachment

SecurityAuditRecord
    └── contains -> ThreatIndicator

ComplianceAuditRecord
    └── governed by -> AuditRetentionPolicy
```

---

# 18. Multi-Tenant Considerations

Tenant-scoped entities:

```text id="r3x9wt"
- AuditRecord
- SecurityAuditRecord
- ComplianceAuditRecord
- SensitiveAccessRecord
- AuditExport
```

---

# 19. Security-Critical Rules

## Immutable Audit Evidence

After persistence:

```text id="c6n1vr"
NO MODIFICATION
```

---

## Sensitive Data Restrictions

Audit entities must never expose:

```text id="w5m8xp"
- Passwords
- Secrets
- Raw tokens
- Sensitive credentials
```

---

## Fail Secure Principle

Audit persistence failures must preserve evidence consistency.

---

# 20. Lifecycle Considerations

| Entity              | Lifecycle           |
| ------------------- | ------------------- |
| AuditRecord         | Long-term           |
| SecurityAuditRecord | Long-term           |
| CorrelationTrace    | Medium-term         |
| AuditExport         | Short-medium        |
| LegalHold           | Regulatory duration |

---

# 21. Future Entity Extensions

Future entities may include:

* BehavioralAuditProfile
* AIThreatPrediction
* ContinuousComplianceSnapshot
* ImmutableLedgerEntry
* PrivacyInvestigationRecord
* DataLineageRecord

---

# 22. Summary

The Audit Management entities provide:

* Immutable audit modeling
* Enterprise-grade traceability
* Security forensic evidence
* Distributed operational tracing
* Compliance-grade retention
* Multi-tenant audit isolation
* Tamper-resistant evidence management

These entities form the operational foundation of the audit ecosystem.

```
```
