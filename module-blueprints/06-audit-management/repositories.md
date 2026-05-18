# 06-audit-management/repositories.md

````md id="y7x3vp"
# Audit Management Repositories

## 1. Introduction

This document defines the repository contracts and persistence responsibilities of the Audit Management module.

Repositories are responsible for:

- Immutable audit persistence
- Security evidence storage
- Compliance traceability
- Distributed trace persistence
- Audit archival
- Retention lifecycle management
- Integrity validation persistence
- Search optimization
- SIEM integration support

The repository layer is designed following:

- Domain-Driven Design (DDD)
- Repository Pattern
- Hexagonal Architecture
- Immutable evidence principles
- Multi-tenant SaaS isolation
- Enterprise compliance standards

---

# 2. Repository Design Principles

| Principle | Description |
|---|---|
| Immutable persistence | Audit evidence cannot change |
| Tenant-aware | Isolation mandatory |
| Append-only preferred | Tamper resistance |
| CQRS-friendly | Optimized read/search |
| Persistence ignorance | Domain isolation |
| High-throughput optimized | Scalable ingestion |
| Security-first | Sensitive evidence protection |

---

# 3. Repository Overview

| Repository | Responsibility |
|---|---|
| AuditRecordRepository | Core immutable audit persistence |
| SecurityAuditRepository | Security evidence persistence |
| ComplianceAuditRepository | Regulatory audit persistence |
| SensitiveAccessRepository | Sensitive access tracking |
| CorrelationTraceRepository | Distributed trace persistence |
| TraceSegmentRepository | Trace segment storage |
| AuditRetentionRepository | Retention lifecycle policies |
| AuditExportRepository | Export lifecycle persistence |
| AuditIntegrityRepository | Tamper-proof validation |
| ThreatIndicatorRepository | Threat evidence persistence |
| LegalHoldRepository | Legal hold lifecycle |
| AuditArchiveRepository | Archive references |
| AuditSearchProjectionRepository | Optimized audit querying |
| SIEMDeliveryRepository | SIEM delivery state tracking |

---

# 4. AuditRecordRepository

## Purpose

Persists immutable audit evidence.

Core repository of the audit domain.

---

## Responsibilities

- Store immutable audit records
- Support audit querying
- Preserve distributed traceability
- Enforce tenant isolation

---

## Example Contract

```java id="m4v8wr"
public interface AuditRecordRepository {

    Mono<AuditRecord> save(
        AuditRecord auditRecord
    );

    Mono<AuditRecord> findById(
        AuditRecordId auditRecordId
    );

    Flux<AuditRecord> search(
        AuditSearchCriteria criteria
    );
}
````

---

## Critical Rules

| Rule                       | Description        |
| -------------------------- | ------------------ |
| Updates forbidden          | Immutable evidence |
| Tenant filtering mandatory | SaaS isolation     |
| Append-only preferred      | Tamper resistance  |

---

# 5. SecurityAuditRepository

## Purpose

Persists security-related audit evidence.

---

## Responsibilities

* Persist threat evidence
* Support incident investigations
* Enable SIEM integrations
* Track suspicious activities

---

## Example Contract

```java id="u7x2vt"
public interface SecurityAuditRepository {

    Mono<SecurityAuditRecord> save(
        SecurityAuditRecord record
    );

    Flux<SecurityAuditRecord> findBySeverity(
        AuditSeverity severity
    );

    Flux<SecurityAuditRecord> findByCorrelationId(
        CorrelationIdentifier correlationId
    );
}
```

---

## Important Constraints

| Constraint                         | Description     |
| ---------------------------------- | --------------- |
| Security evidence immutable        | Forensics       |
| High-severity indexing recommended | Threat response |

---

# 6. ComplianceAuditRepository

## Purpose

Persists compliance-grade audit evidence.

---

## Responsibilities

* Track regulatory operations
* Support compliance reporting
* Enforce retention linkage

---

## Example Contract

```java id="r2m9xp"
public interface ComplianceAuditRepository {

    Mono<ComplianceAuditRecord> save(
        ComplianceAuditRecord record
    );

    Flux<ComplianceAuditRecord> findByRegulation(
        ComplianceClassification regulation
    );
}
```

---

## Compliance Rules

* Long-term retention supported
* Legal evidence immutable

---

# 7. SensitiveAccessRepository

## Purpose

Tracks sensitive resource access.

Critical for medical/legal systems.

---

## Responsibilities

* Persist sensitive access records
* Support privacy investigations
* Enable access reconstruction

---

## Example Contract

```java id="g8v4wr"
public interface SensitiveAccessRepository {

    Mono<SensitiveAccessRecord> save(
        SensitiveAccessRecord record
    );

    Flux<SensitiveAccessRecord> findByResource(
        ResourceIdentifier resource
    );
}
```

---

## Security Rules

| Rule                      | Description |
| ------------------------- | ----------- |
| Strict tenant isolation   | Privacy     |
| Immutable access evidence | Compliance  |

---

# 8. CorrelationTraceRepository

## Purpose

Persists distributed trace structures.

---

## Responsibilities

* Store distributed traces
* Support timeline reconstruction
* Correlate cross-service events

---

## Example Contract

```java id="p5x1vt"
public interface CorrelationTraceRepository {

    Mono<CorrelationTrace> save(
        CorrelationTrace trace
    );

    Mono<CorrelationTrace> findByCorrelationId(
        CorrelationIdentifier correlationId
    );
}
```

---

## Recommended Optimization

Index:

```text id="f9m7wr"
correlationId
```

for forensic performance.

---

# 9. TraceSegmentRepository

## Purpose

Stores distributed service segments.

---

## Responsibilities

* Persist service hops
* Support distributed diagnostics
* Reconstruct operational timelines

---

## Example Contract

```java id="t3v8xp"
public interface TraceSegmentRepository {

    Mono<TraceSegment> save(
        TraceSegment segment
    );

    Flux<TraceSegment> findByTrace(
        CorrelationTraceId traceId
    );
}
```

---

# 10. AuditRetentionRepository

## Purpose

Persists retention lifecycle rules.

---

## Responsibilities

* Store retention policies
* Resolve expiration logic
* Support archival workflows

---

## Example Contract

```java id="x6m2wr"
public interface AuditRetentionRepository {

    Mono<AuditRetentionPolicy> save(
        AuditRetentionPolicy policy
    );

    Flux<AuditRetentionPolicy> findActivePolicies();
}
```

---

## Important Rules

* Retention policy changes auditable
* Legal holds override expiration

---

# 11. AuditExportRepository

## Purpose

Persists export lifecycle evidence.

---

## Responsibilities

* Track exports
* Persist export metadata
* Support compliance evidence

---

## Example Contract

```java id="d1x9vt"
public interface AuditExportRepository {

    Mono<AuditExport> save(
        AuditExport export
    );

    Mono<AuditExport> findById(
        AuditExportId exportId
    );
}
```

---

## Security Rules

* Exports auditable
* Export authorization mandatory

---

# 12. AuditIntegrityRepository

## Purpose

Persists integrity validation evidence.

---

## Responsibilities

* Store integrity hashes
* Validate tamper resistance
* Support forensic verification

---

## Example Contract

```java id="n7v3wr"
public interface AuditIntegrityRepository {

    Mono<AuditIntegrityProof> save(
        AuditIntegrityProof proof
    );

    Mono<AuditIntegrityProof> findByAuditRecord(
        AuditRecordId auditRecordId
    );
}
```

---

## Critical Constraints

| Constraint                 | Description       |
| -------------------------- | ----------------- |
| Integrity proofs immutable | Tamper resistance |
| Verification reproducible  | Forensics         |

---

# 13. ThreatIndicatorRepository

## Purpose

Persists threat detection evidence.

---

## Responsibilities

* Store threat indicators
* Support threat analytics
* Enable incident escalation

---

## Example Contract

```java id="v2m8xp"
public interface ThreatIndicatorRepository {

    Mono<ThreatIndicator> save(
        ThreatIndicator indicator
    );

    Flux<ThreatIndicator> findHighSeverityThreats();
}
```

---

# 14. LegalHoldRepository

## Purpose

Persists legal retention holds.

---

## Responsibilities

* Apply legal holds
* Prevent expiration
* Support legal investigations

---

## Example Contract

```java id="k5x1wr"
public interface LegalHoldRepository {

    Mono<LegalHold> save(
        LegalHold hold
    );

    Flux<LegalHold> findActiveHolds();
}
```

---

## Important Rules

* Holds override deletion/expiration
* Hold lifecycle auditable

---

# 15. AuditArchiveRepository

## Purpose

Persists archival references.

---

## Responsibilities

* Store archive references
* Support restoration
* Track archive lifecycle

---

## Example Contract

```java id="q9v4xt"
public interface AuditArchiveRepository {

    Mono<AuditArchiveReference> save(
        AuditArchiveReference archive
    );

    Mono<AuditArchiveReference> findById(
        ArchiveReferenceId archiveId
    );
}
```

---

# 16. AuditSearchProjectionRepository

## Purpose

Provides optimized search/read models.

CQRS-oriented repository.

---

## Responsibilities

* Audit search optimization
* Full-text indexing
* Timeline querying
* Dashboard projections

---

## Example Contract

```java id="w3m7vp"
public interface AuditSearchProjectionRepository {

    Flux<AuditSearchProjection> search(
        AuditSearchCriteria criteria
    );
}
```

---

## Recommended Technologies

| Technology    | Suitability            |
| ------------- | ---------------------- |
| Elasticsearch | Full-text audit search |
| OpenSearch    | Distributed querying   |

---

# 17. SIEMDeliveryRepository

## Purpose

Tracks SIEM integration delivery state.

---

## Responsibilities

* Track delivery attempts
* Support retry orchestration
* Persist delivery failures

---

## Example Contract

```java id="f8x2wr"
public interface SIEMDeliveryRepository {

    Mono<SIEMDeliveryState> save(
        SIEMDeliveryState state
    );

    Flux<SIEMDeliveryState> findFailedDeliveries();
}
```

---

# 18. Multi-Tenant Repository Rules

## Mandatory Tenant Isolation

Repositories must enforce:

```sql id="m1v9xt"
WHERE tenant_id = :tenantId
```

---

## Forbidden Behavior

```text id="r6x4wp"
Cross-tenant audit visibility
```

---

# 19. Persistence Strategies

| Aggregate                 | Strategy                  |
| ------------------------- | ------------------------- |
| AuditRecordAggregate      | Append-only relational    |
| SecurityAuditAggregate    | Indexed relational/search |
| CorrelationTraceAggregate | Distributed indexing      |
| AuditExportAggregate      | Relational tracking       |
| AuditArchiveAggregate     | Object storage references |

---

# 20. Recommended Database Technologies

| Technology        | Use Case                    |
| ----------------- | --------------------------- |
| PostgreSQL        | Immutable audit persistence |
| Elasticsearch     | Search and forensics        |
| Kafka             | Event streaming             |
| S3/Object Storage | Long-term archival          |
| Redis             | Hot trace caching           |

---

# 21. CQRS Considerations

Recommended separation:

## Write Side

* Immutable persistence
* Evidence integrity
* Retention enforcement

---

## Read Side

* Search projections
* Timeline reconstruction
* Security dashboards
* Compliance reporting

---

# 22. Reactive Repository Considerations

Reactive support strongly recommended.

---

## Example

```java id="u4m8vr"
Mono<AuditRecord>
Flux<SecurityAuditRecord>
```

---

## Benefits

| Benefit                   | Description         |
| ------------------------- | ------------------- |
| High ingestion throughput | Scalability         |
| Non-blocking IO           | Performance         |
| Async streaming           | Distributed systems |

---

# 23. Transaction Management

## Strong Consistency Required

| Operation                  | Reason             |
| -------------------------- | ------------------ |
| Audit persistence          | Evidence integrity |
| Integrity proof generation | Tamper protection  |
| Legal hold application     | Compliance         |

---

## Eventual Consistency Acceptable

| Operation             | Reason             |
| --------------------- | ------------------ |
| Search indexing       | Query optimization |
| SIEM forwarding       | Async integration  |
| Analytics projections | Reporting          |

---

# 24. Security-Critical Repository Rules

## Audit Modification Forbidden

After persistence:

```text id="c7v1xp"
NO UPDATE
```

---

## Sensitive Data Restrictions

Repositories must never persist:

```text id="g2m9wr"
- Passwords
- Secrets
- Raw JWTs
- MFA secrets
```

---

## Fail Secure Principle

Repository failures must preserve evidence integrity.

---

# 25. Performance Considerations

Critical performance areas:

| Area               | Optimization            |
| ------------------ | ----------------------- |
| Audit ingestion    | Batch streaming         |
| Correlation lookup | Indexed correlation IDs |
| Security searches  | Elasticsearch           |
| Archive retrieval  | Async restoration       |

---

# 26. Indexing Recommendations

| Table            | Recommended Index       |
| ---------------- | ----------------------- |
| audit_records    | tenant_id + occurred_at |
| security_audit   | severity + occurred_at  |
| traces           | correlation_id          |
| sensitive_access | resource_id             |

---

# 27. Soft Delete Strategy

Soft delete is NOT recommended for immutable evidence.

Preferred strategy:

```text id="j5x8vt"
ARCHIVAL
instead of deletion
```

---

# 28. Distributed System Considerations

Repositories must support:

* Multi-region deployments
* Distributed event ingestion
* Horizontal scalability
* Reactive streaming
* Eventual consistency projections

---

# 29. Future Repository Extensions

Future repositories may include:

* BehavioralAuditRepository
* AIThreatAnalysisRepository
* ImmutableLedgerRepository
* PrivacyInvestigationRepository
* ComplianceSnapshotRepository

---

# 30. Summary

The Audit Management repositories provide:

* Immutable audit persistence
* Enterprise-grade forensic traceability
* Distributed operational correlation
* Compliance-grade evidence retention
* Reactive audit scalability
* Multi-tenant audit isolation
* Tamper-resistant audit integrity

These repositories form the persistence backbone of the audit ecosystem.

```
```
