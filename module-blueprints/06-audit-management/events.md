# 06-audit-management/events.md

````md id="q7x4vp"
# Audit Management Domain Events

## 1. Introduction

This document defines the domain events emitted and consumed by the Audit Management module.

Audit events represent important occurrences related to:

- Audit evidence creation
- Security investigations
- Compliance traceability
- Distributed correlation
- Retention lifecycle management
- Audit archival
- Tamper detection
- Export generation
- Threat analysis

These events are fundamental for:

- Event-Driven Architecture (EDA)
- Distributed observability
- Compliance traceability
- Security forensics
- SIEM integrations
- Incident response
- Reactive audit pipelines

The events are designed following:

- Domain-Driven Design (DDD)
- Immutable event principles
- Multi-tenant SaaS isolation
- Enterprise auditability standards

---

# 2. Event Design Principles

All audit events must follow:

| Principle | Description |
|---|---|
| Immutable | Events cannot change |
| Auditable | Full traceability |
| Tenant-aware | Tenant isolation mandatory |
| Serializable | Messaging compatibility |
| Security-safe | Sensitive data restricted |
| Correlated | Distributed trace support |

---

# 3. Event Categories

| Category | Purpose |
|---|---|
| Audit Events | Core audit lifecycle |
| Security Audit Events | Threat and security evidence |
| Compliance Events | Regulatory operations |
| Retention Events | Lifecycle management |
| Correlation Events | Distributed tracing |
| Export Events | Compliance exports |
| Integrity Events | Tamper detection |
| Integration Events | SIEM/observability integrations |

---

# 4. Common Event Metadata

All audit events should include:

| Field | Type | Description |
|---|---|---|
| eventId | UUID | Unique identifier |
| eventType | String | Event name |
| occurredAt | Instant | Event timestamp |
| tenantId | String | Tenant context |
| actorId | UUID | User/service actor |
| correlationId | String | Distributed trace |
| aggregateId | UUID | Aggregate identifier |
| aggregateType | String | Aggregate type |
| version | Integer | Event schema version |

---

# 5. AuditRecordCreated Event

## Purpose

Published after immutable audit persistence.

---

## Trigger

```text id="m2v8wr"
New audit evidence persisted
````

---

## Payload

| Field         | Type   | Description      |
| ------------- | ------ | ---------------- |
| auditRecordId | UUID   | Audit identifier |
| category      | String | Audit category   |
| action        | String | Audited action   |
| resourceType  | String | Target resource  |
| result        | String | SUCCESS/FAILURE  |

---

## Consumers

* SIEM systems
* Analytics engines
* Compliance monitoring
* Threat analysis pipelines

---

# 6. SecurityAuditRecorded Event

## Purpose

Published after security audit creation.

---

## Payload

| Field                 | Type   | Description         |
| --------------------- | ------ | ------------------- |
| securityAuditRecordId | UUID   | Security audit ID   |
| severity              | String | LOW/HIGH/etc        |
| threatType            | String | Threat category     |
| evidence              | Object | Supporting metadata |

---

## Example Threats

```text id="x7n3vp"
- TOKEN_REPLAY
- MFA_FAILURE
- CROSS_TENANT_ACCESS
```

---

## Critical Consumers

* SIEM
* Threat detection engines
* Incident response systems

---

# 7. ComplianceAuditRecorded Event

## Purpose

Published after compliance evidence persistence.

---

## Payload

| Field                   | Type   | Description          |
| ----------------------- | ------ | -------------------- |
| complianceAuditRecordId | UUID   | Compliance audit ID  |
| regulationType          | String | HIPAA/GDPR/etc       |
| complianceCategory      | String | Audit classification |

---

## Example Use Cases

```text id="u5m9xt"
- Medical record access
- Consent export
- Privacy request handling
```

---

# 8. SensitiveAccessDetected Event

## Purpose

Published when sensitive resources are accessed.

Critical medical/legal event.

---

## Payload

| Field          | Type   | Description        |
| -------------- | ------ | ------------------ |
| accessRecordId | UUID   | Access audit ID    |
| actorId        | UUID   | Accessing user     |
| resourceType   | String | Sensitive resource |
| accessReason   | String | Declared rationale |

---

## Example Resources

```text id="r1x6wr"
- Medical records
- Psychological evaluations
- Consent forms
```

---

# 9. DistributedTraceLinked Event

## Purpose

Published when distributed trace segments are correlated.

---

## Payload

| Field         | Type   | Description       |
| ------------- | ------ | ----------------- |
| correlationId | String | Distributed trace |
| serviceName   | String | Origin service    |
| operation     | String | Trace operation   |

---

## Usage

Supports:

* Timeline reconstruction
* Cross-service tracing
* Incident forensics

---

# 10. ThreatIndicatorDetected Event

## Purpose

Published when threat indicators are identified.

---

## Example Threats

```text id="v4k8wp"
- Impossible travel
- Token replay
- Excessive failures
- Privilege escalation
```

---

## Payload

| Field             | Type   | Description         |
| ----------------- | ------ | ------------------- |
| threatIndicatorId | UUID   | Threat ID           |
| severity          | String | Risk severity       |
| detectionSource   | String | Detection mechanism |

---

## Side Effects

```text id="j9m2vr"
- Alert generation
- Incident escalation
- SIEM forwarding
```

---

# 11. AuditIntegrityProofGenerated Event

## Purpose

Published after integrity proof generation.

---

## Payload

| Field            | Type   | Description      |
| ---------------- | ------ | ---------------- |
| integrityProofId | UUID   | Proof identifier |
| auditRecordId    | UUID   | Protected audit  |
| algorithm        | String | Hash algorithm   |

---

## Importance

Supports tamper-resistant evidence validation.

---

# 12. AuditIntegrityViolationDetected Event

## Purpose

Published when tampering is detected.

High-severity event.

---

## Trigger Examples

```text id="g6x1vt"
- Hash mismatch
- Missing audit segment
- Integrity chain corruption
```

---

## Recommended Actions

```text id="p3v9wr"
- Immediate investigation
- Evidence preservation
- Security escalation
```

---

# 13. AuditRetentionExpired Event

## Purpose

Published when audit retention expires.

---

## Payload

| Field             | Type | Description    |
| ----------------- | ---- | -------------- |
| auditRecordId     | UUID | Expired audit  |
| retentionPolicyId | UUID | Applied policy |

---

## Restrictions

Legal holds override expiration.

---

# 14. LegalHoldApplied Event

## Purpose

Published after legal hold activation.

---

## Payload

| Field       | Type   | Description       |
| ----------- | ------ | ----------------- |
| legalHoldId | UUID   | Hold identifier   |
| holdReason  | String | Legal rationale   |
| appliedBy   | UUID   | Responsible actor |

---

## Importance

Prevents evidence expiration/deletion.

---

# 15. LegalHoldReleased Event

## Purpose

Published after legal hold removal.

---

## Side Effects

```text id="n8m4xp"
Retention lifecycle resumes
```

---

# 16. AuditArchived Event

## Purpose

Published after archival completion.

---

## Payload

| Field              | Type   | Description       |
| ------------------ | ------ | ----------------- |
| archiveReferenceId | UUID   | Archive reference |
| archiveLocation    | String | Storage location  |

---

## Usage

Supports:

* Long-term retention
* Compliance preservation
* Disaster recovery

---

# 17. AuditRestored Event

## Purpose

Published after archived evidence restoration.

---

## Usage

Supports:

* Investigations
* Compliance requests
* Legal review

---

# 18. AuditExportRequested Event

## Purpose

Published when audit export begins.

---

## Payload

| Field        | Type   | Description       |
| ------------ | ------ | ----------------- |
| exportId     | UUID   | Export identifier |
| exportFormat | String | CSV/PDF/etc       |
| requestedBy  | UUID   | Requesting actor  |

---

## Security Rules

* Authorization mandatory
* Export operations auditable

---

# 19. AuditExportCompleted Event

## Purpose

Published after successful export generation.

---

## Side Effects

```text id="t2x7wr"
- Compliance evidence
- Export audit persistence
- Delivery notifications
```

---

# 20. AuditExportFailed Event

## Purpose

Published when export generation fails.

---

## Usage

Supports:

* Operational monitoring
* Retry orchestration
* Incident tracking

---

# 21. CrossTenantAccessDetected Event

## Purpose

Published after tenant isolation violation attempts.

Critical security event.

---

## Payload

| Field        | Type   | Description       |
| ------------ | ------ | ----------------- |
| sourceTenant | String | Origin tenant     |
| targetTenant | String | Attempted tenant  |
| actorId      | UUID   | Responsible actor |

---

## Recommended Actions

```text id="f5v1xp"
- Immediate escalation
- Security investigation
- Threat analysis
```

---

# 22. SIEMForwardingFailed Event

## Purpose

Published when SIEM delivery fails.

---

## Usage

Supports:

* Retry orchestration
* Monitoring alerts
* Observability resilience

---

# 23. CorrelationTimelineReconstructed Event

## Purpose

Published after successful forensic reconstruction.

---

## Usage

Supports:

* Incident analysis
* Operational tracing
* Investigation reporting

---

# 24. AuditSearchExecuted Event

## Purpose

Published after audit search execution.

---

## Usage

Supports:

* Monitoring
* Search analytics
* Compliance traceability

---

## Important Restriction

Search metadata must avoid sensitive overexposure.

---

# 25. AuditPolicyChanged Event

## Purpose

Published after retention/compliance policy updates.

---

## Example Changes

```text id="w9m3vt"
- Retention duration changes
- Compliance scope updates
- Archival policy modifications
```

---

## Side Effects

```text id="d4x8wr"
- Lifecycle recalculation
- Archival reevaluation
```

---

# 26. Event Ordering Considerations

Certain events require ordering guarantees.

---

## Example

```text id="u7n2vp"
AuditRecordCreated
    before
AuditIntegrityProofGenerated
```

---

## Recommended Strategies

| Strategy           | Purpose              |
| ------------------ | -------------------- |
| Kafka partitioning | Tenant ordering      |
| Outbox pattern     | Reliable publishing  |
| Aggregate ordering | Evidence consistency |

---

# 27. Event Delivery Guarantees

Recommended semantics:

| Event Type            | Guarantee              |
| --------------------- | ---------------------- |
| Security audit events | At least once          |
| Compliance events     | Durable delivery       |
| SIEM integrations     | Retry mandatory        |
| Export notifications  | Best effort acceptable |

---

# 28. Sensitive Data Restrictions

Audit events must NEVER expose:

* Passwords
* Secrets
* MFA tokens
* Raw JWTs
* Sensitive credentials

---

# 29. Recommended Messaging Infrastructure

| Technology    | Suitability                |
| ------------- | -------------------------- |
| Kafka         | High-scale event streaming |
| RabbitMQ      | Routing flexibility        |
| Redis Streams | Lightweight streaming      |
| AWS SNS/SQS   | Cloud-native eventing      |

---

# 30. Replay and Reconstruction Considerations

Replay-compatible events:

| Event                  | Purpose                 |
| ---------------------- | ----------------------- |
| AuditRecordCreated     | Timeline reconstruction |
| SecurityAuditRecorded  | Threat analysis         |
| DistributedTraceLinked | Cross-service tracing   |

---

# 31. CQRS Integration

Events may update projections including:

* Security dashboards
* Compliance dashboards
* Threat analytics
* Audit search indexes
* Timeline reconstruction views

---

# 32. Distributed System Considerations

Events support:

* Multi-region deployments
* Horizontal scaling
* Reactive architectures
* Distributed observability
* Eventual consistency

---

# 33. Failure Handling Rules

If event publication fails:

| Event Type        | Strategy                     |
| ----------------- | ---------------------------- |
| Security events   | Retry mandatory              |
| Compliance events | Durable persistence required |
| Search analytics  | Retry optional               |

---

# 34. Future Event Extensions

Future events may include:

* AIThreatDetected
* BehavioralAnomalyDetected
* ContinuousComplianceViolationDetected
* ImmutableLedgerCommitted
* PrivacyInvestigationStarted
* DataLineageCorrelated

---

# 35. Summary

The Audit Management events provide:

* Enterprise-grade audit traceability
* Security forensic observability
* Distributed correlation support
* Compliance-grade evidence streaming
* Reactive audit integrations
* Multi-tenant audit isolation
* Tamper-resistant event-driven accountability

These events form the integration backbone of the audit ecosystem.

```
```
