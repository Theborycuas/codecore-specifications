# 06-audit-management/api-contracts.md

````md id="n4x8vp"
# Audit Management API Contracts

## 1. Introduction

This document defines the API contracts of the Audit Management module.

The APIs expose audit capabilities including:

- Audit search
- Security audit investigation
- Compliance evidence retrieval
- Distributed trace inspection
- Audit exports
- Retention management
- Integrity validation
- Forensic analysis
- SIEM integrations

The contracts are designed following:

- RESTful principles
- Secure-by-default architecture
- Multi-tenant SaaS isolation
- Zero Trust security principles
- Enterprise compliance standards

---

# 2. API Design Principles

| Principle | Description |
|---|---|
| Immutable audit evidence | No modification APIs |
| Tenant-aware access | Isolation mandatory |
| Least privilege | Restricted visibility |
| Secure exports | Controlled evidence delivery |
| Auditable access | Audit reads tracked |
| Versioned contracts | Backward compatibility |
| Reactive-friendly | High-throughput support |

---

# 3. Base URL

```text id="u7m2wr"
/api/v1/audit
````

---

# 4. Common Headers

| Header           | Required    | Description         |
| ---------------- | ----------- | ------------------- |
| Authorization    | Yes         | Bearer JWT          |
| X-Tenant-ID      | Yes         | Tenant context      |
| X-Correlation-ID | Recommended | Distributed tracing |
| Content-Type     | Yes         | application/json    |

---

# 5. Audit Search APIs

# 5.1 Search Audit Records

## Endpoint

```text id="r5x9vt"
GET /records
```

---

## Purpose

Searches immutable audit evidence.

---

## Query Parameters

| Parameter     | Description             |
| ------------- | ----------------------- |
| category      | SECURITY/COMPLIANCE/etc |
| actorId       | User/service actor      |
| action        | Audited action          |
| resourceType  | Target resource         |
| resourceId    | Resource identifier     |
| result        | SUCCESS/FAILURE         |
| startDate     | Start timestamp         |
| endDate       | End timestamp           |
| correlationId | Distributed trace       |

---

## Example Request

```text id="g2v8wr"
/api/v1/audit/records?category=SECURITY
```

---

## Example Response

```json id="m8n4xp"
{
  "success": true,
  "data": [
    {
      "auditRecordId": "uuid",
      "action": "LOGIN_SUCCESS",
      "actorId": "uuid",
      "occurredAt": "2026-05-18T10:00:00Z"
    }
  ]
}
```

---

## Security Rules

* Tenant filtering mandatory
* Sensitive filtering required
* Search access auditable

---

# 5.2 Retrieve Audit Record

## Endpoint

```text id="p1x7vt"
GET /records/{auditRecordId}
```

---

## Purpose

Retrieves immutable audit evidence.

---

## Security Rules

* Tenant ownership validation mandatory
* Sensitive metadata filtering required

---

# 6. Security Audit APIs

# 6.1 Search Security Audits

## Endpoint

```text id="w9m3xp"
GET /security
```

---

## Query Parameters

| Parameter     | Description     |
| ------------- | --------------- |
| severity      | LOW/HIGH/etc    |
| threatType    | Threat category |
| actorId       | User/service    |
| ipAddress     | Network origin  |
| correlationId | Trace linkage   |

---

## Example Threat Types

```text id="f6x2wr"
TOKEN_REPLAY
PRIVILEGE_ESCALATION
MFA_FAILURE
```

---

## Required Permission

```text id="t4v8wp"
VIEW_SECURITY_AUDIT
```

---

# 6.2 Retrieve Security Investigation Timeline

## Endpoint

```text id="x7n1vr"
GET /security/timeline/{correlationId}
```

---

## Purpose

Reconstructs distributed security timeline.

---

## Example Response

```json id="k3m9xt"
{
  "success": true,
  "data": {
    "correlationId": "corr-123",
    "events": []
  }
}
```

---

## Usage

Supports:

* Incident response
* Threat investigations
* Forensic reconstruction

---

# 7. Compliance Audit APIs

# 7.1 Search Compliance Audits

## Endpoint

```text id="d8v4xp"
GET /compliance
```

---

## Query Parameters

| Parameter          | Description       |
| ------------------ | ----------------- |
| regulationType     | HIPAA/GDPR/etc    |
| complianceCategory | Access/export/etc |
| actorId            | Responsible actor |

---

## Required Permission

```text id="q2x7wt"
VIEW_COMPLIANCE_AUDIT
```

---

# 7.2 Retrieve Sensitive Access History

## Endpoint

```text id="u5m1vr"
GET /sensitive-access/{resourceId}
```

---

## Purpose

Retrieves access history for sensitive resources.

---

## Example Resources

```text id="y8v3xp"
- Patient records
- Clinical evaluations
- Consent forms
```

---

## Security Rules

* Strict authorization mandatory
* Access reason visibility controlled

---

# 8. Correlation Trace APIs

# 8.1 Retrieve Distributed Trace

## Endpoint

```text id="n6x9wr"
GET /traces/{correlationId}
```

---

## Purpose

Retrieves distributed operational trace.

---

## Example Response

```json id="h1v7xt"
{
  "success": true,
  "data": {
    "correlationId": "corr-123",
    "segments": []
  }
}
```

---

## Benefits

| Benefit                  | Description         |
| ------------------------ | ------------------- |
| Cross-service visibility | Distributed tracing |
| Timeline reconstruction  | Investigations      |
| Operational diagnostics  | Observability       |

---

# 9. Audit Export APIs

# 9.1 Generate Audit Export

## Endpoint

```text id="c5m8vp"
POST /exports
```

---

## Purpose

Generates compliance-grade audit export.

---

## Request

```json id="r3x4wt"
{
  "format": "PDF",
  "category": "SECURITY",
  "startDate": "2026-01-01",
  "endDate": "2026-05-01"
}
```

---

## Response

```json id="g7v2xr"
{
  "success": true,
  "data": {
    "exportId": "uuid",
    "status": "PENDING"
  }
}
```

---

## Security Rules

* Export authorization mandatory
* Export actions auditable
* Sensitive filtering enforced

---

# 9.2 Download Audit Export

## Endpoint

```text id="m9x1vp"
GET /exports/{exportId}/download
```

---

## Purpose

Downloads generated export.

---

## Security Rules

* Ownership validation mandatory
* Temporary signed URLs recommended

---

# 10. Retention APIs

# 10.1 List Retention Policies

## Endpoint

```text id="t2v8wr"
GET /retention-policies
```

---

## Purpose

Lists audit retention configurations.

---

## Required Permission

```text id="p6n3xt"
VIEW_RETENTION_POLICIES
```

---

# 10.2 Create Retention Policy

## Endpoint

```text id="v4x7wp"
POST /retention-policies
```

---

## Request

```json id="f1m9vr"
{
  "policyName": "Security Retention",
  "retentionPeriodDays": 2555
}
```

---

## Security Rules

* Administrative authorization mandatory
* Policy changes auditable

---

# 11. Legal Hold APIs

# 11.1 Apply Legal Hold

## Endpoint

```text id="x8n2vt"
POST /legal-holds
```

---

## Purpose

Prevents audit expiration/deletion.

---

## Request

```json id="j4v7wr"
{
  "reason": "Ongoing investigation",
  "resourceId": "audit-123"
}
```

---

## Security Rules

* Legal authorization mandatory
* Hold actions immutable

---

# 11.2 Release Legal Hold

## Endpoint

```text id="q5x1vp"
DELETE /legal-holds/{legalHoldId}
```

---

## Security Rules

* Strict authorization required
* Release actions auditable

---

# 12. Integrity Validation APIs

# 12.1 Validate Audit Integrity

## Endpoint

```text id="w7m4xt"
POST /integrity/validate
```

---

## Purpose

Verifies tamper resistance.

---

## Request

```json id="u3x9wr"
{
  "auditRecordId": "uuid"
}
```

---

## Response

```json id="g6v2wp"
{
  "success": true,
  "data": {
    "valid": true
  }
}
```

---

## Failure Example

```json id="n1m8vr"
{
  "success": false,
  "error": {
    "code": "INTEGRITY_VIOLATION"
  }
}
```

---

# 13. Archive APIs

# 13.1 Retrieve Archived Audit Reference

## Endpoint

```text id="r9x4vt"
GET /archives/{archiveReferenceId}
```

---

## Purpose

Retrieves archived evidence metadata.

---

## Security Rules

* Compliance authorization required
* Restoration actions auditable

---

# 14. SIEM Integration APIs

# 14.1 Stream Security Events

## Endpoint

```text id="k2v7wr"
GET /stream/security
```

---

## Purpose

Streams security audit events.

---

## Recommended Technologies

| Technology   | Recommendation        |
| ------------ | --------------------- |
| SSE          | Lightweight streaming |
| WebSocket    | Realtime integrations |
| Kafka bridge | High-scale SIEM       |

---

## Security Rules

* Restricted integration access
* Token-based service authorization

---

# 15. Health and Monitoring APIs

# 15.1 Audit Pipeline Health

## Endpoint

```text id="f8m3xp"
GET /health
```

---

## Example Response

```json id="p5x9wr"
{
  "status": "UP",
  "integrations": {
    "siem": "UP",
    "archive": "UP"
  }
}
```

---

# 16. Common Response Structure

## Success Response

```json id="y1v6xt"
{
  "success": true,
  "timestamp": "2026-05-18T10:00:00Z",
  "data": {}
}
```

---

## Error Response

```json id="t4m8vp"
{
  "success": false,
  "timestamp": "2026-05-18T10:00:00Z",
  "error": {
    "code": "AUDIT_NOT_FOUND",
    "message": "Audit record not found"
  }
}
```

---

# 17. HTTP Status Codes

| Status | Meaning             |
| ------ | ------------------- |
| 200    | Success             |
| 201    | Resource created    |
| 204    | No content          |
| 400    | Invalid request     |
| 401    | Unauthenticated     |
| 403    | Unauthorized        |
| 404    | Resource not found  |
| 409    | Conflict            |
| 422    | Validation error    |
| 429    | Rate limit exceeded |
| 500    | Internal error      |

---

# 18. Pagination Standards

Paginated endpoints should return:

```json id="d7v2xr"
{
  "success": true,
  "data": [],
  "pagination": {
    "page": 0,
    "size": 20,
    "totalElements": 100
  }
}
```

---

# 19. Sorting Standards

## Example

```text id="m3x8wr"
?sort=occurredAt,desc
```

---

# 20. Security Headers

Recommended headers:

| Header           | Purpose          |
| ---------------- | ---------------- |
| Authorization    | Identity         |
| X-Tenant-ID      | Tenant isolation |
| X-Correlation-ID | Traceability     |
| X-Request-ID     | Request tracing  |

---

# 21. API Security Rules

## Immutable Audit Principle

Forbidden operations:

```text id="u6n1vp"
- Audit modification
- Audit deletion
```

---

## Tenant Isolation

Tenant mismatch:

```text id="q8v4xt"
403 FORBIDDEN
```

---

## Sensitive Data Restrictions

Never expose:

* Passwords
* Secrets
* Raw tokens
* Sensitive credentials

---

# 22. Reactive API Considerations

Reactive implementations should support:

```text id="w2m9vr"
Mono<ResponseEntity<?>>
Flux<ResponseEntity<?>>
```

---

## Requirements

* Non-blocking search
* Reactive export streaming
* Async archival retrieval

---

# 23. OpenAPI Recommendations

Recommended documentation:

* OpenAPI 3.x
* Swagger UI
* Security scheme definitions
* Audit schema examples

---

# 24. API Versioning Strategy

Recommended:

```text id="x5v7wp"
/api/v1/audit
```

Future evolution:

```text id="c9n3xt"
/api/v2/audit
```

---

# 25. Error Codes

| Code                     | Description               |
| ------------------------ | ------------------------- |
| AUDIT_NOT_FOUND          | Missing audit record      |
| INTEGRITY_VIOLATION      | Tampering detected        |
| EXPORT_FAILED            | Export generation failure |
| LEGAL_HOLD_REQUIRED      | Resource protected        |
| INVALID_RETENTION_POLICY | Invalid lifecycle         |
| TENANT_MISMATCH          | Cross-tenant violation    |
| UNAUTHORIZED_EXPORT      | Export denied             |

---

# 26. Future API Extensions

Future APIs may include:

* AI threat investigation APIs
* Behavioral audit APIs
* Continuous compliance APIs
* Immutable ledger APIs
* Privacy investigation APIs

---

# 27. Summary

The Audit Management API contracts provide:

* Enterprise-grade audit querying
* Immutable evidence retrieval
* Distributed forensic tracing
* Compliance-grade export management
* Reactive audit scalability
* Multi-tenant audit isolation
* Tamper-resistant audit validation

These APIs form the external contract layer of the audit ecosystem.

```
```
