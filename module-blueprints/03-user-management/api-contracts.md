# api-contracts.md

````md id="userapicontracts01"
# User Management
## API Contract Standards
### CodeCore Module Blueprints
### Version 1.0

---

# 1. PURPOSE

This document defines the official API contract standards for the User Management bounded context.

Its objectives are:

- standardize operational user APIs
- preserve tenant-safe participation
- enforce ownership consistency
- support scalable organizational execution
- preserve reactive-safe communication
- guarantee contract consistency
- preserve observability and auditability
- guide AI-assisted implementation

---

# 2. API PHILOSOPHY

User Management APIs exist to:
- expose operational actor capabilities
- coordinate organizational participation
- preserve tenant-safe ownership
- support scalable organizational execution
- propagate contextual operational metadata

User APIs MUST:
- remain reactive
- remain tenant-aware
- remain observable
- remain auditable
- preserve organizational consistency

---

# 3. OFFICIAL API STRATEGY

User Management officially adopts:

```text id="officialuserapistrategy"
REST + JSON + Reactive APIs
````

---

# 3.1 Base Paths

Recommended base paths:

```text id="userapibasepaths"
/api/v1/users
/api/v1/memberships
/api/v1/actors
/api/v1/organization-units
/api/v1/ownerships
```

---

# 3.2 Reactive Contract Principle

All User APIs MUST:

* remain non-blocking
* support Reactor-based execution
* preserve async-safe communication

---

# 4. USER PROFILE ENDPOINTS

---

# 4.1 Official User Profile Endpoints

| Endpoint                 | Method | Purpose                   |
| ------------------------ | ------ | ------------------------- |
| /users                   | POST   | Register operational user |
| /users/{userId}          | GET    | Retrieve user profile     |
| /users/{userId}          | PUT    | Update user profile       |
| /users/{userId}/activate | POST   | Activate profile          |
| /users/{userId}/suspend  | POST   | Suspend profile           |
| /users/{userId}/archive  | POST   | Archive profile           |

---

# 4.2 User Preferences Endpoints

| Endpoint                    | Method | Purpose              |
| --------------------------- | ------ | -------------------- |
| /users/{userId}/preferences | GET    | Retrieve preferences |
| /users/{userId}/preferences | PUT    | Update preferences   |

---

# 4.3 User Contact Endpoints

| Endpoint                 | Method | Purpose                      |
| ------------------------ | ------ | ---------------------------- |
| /users/{userId}/contacts | GET    | Retrieve contact information |
| /users/{userId}/contacts | PUT    | Update contact information   |

---

# 5. MEMBERSHIP ENDPOINTS

---

# 5.1 Official Membership Endpoints

| Endpoint                             | Method | Purpose             |
| ------------------------------------ | ------ | ------------------- |
| /memberships                         | POST   | Create membership   |
| /memberships/{membershipId}          | GET    | Retrieve membership |
| /memberships/{membershipId}/activate | POST   | Activate membership |
| /memberships/{membershipId}/suspend  | POST   | Suspend membership  |
| /memberships/{membershipId}/archive  | POST   | Archive membership  |

---

# 5.2 Branch Membership Endpoints

| Endpoint                                        | Method | Purpose           |
| ----------------------------------------------- | ------ | ----------------- |
| /memberships/{membershipId}/branches            | POST   | Assign branch     |
| /memberships/{membershipId}/branches/{branchId} | DELETE | Remove branch     |
| /memberships/{membershipId}/branches            | GET    | Retrieve branches |

---

# 6. ACTOR ENDPOINTS

---

# 6.1 Official Actor Endpoints

| Endpoint                   | Method | Purpose                     |
| -------------------------- | ------ | --------------------------- |
| /actors                    | POST   | Create actor                |
| /actors/{actorId}          | GET    | Retrieve actor              |
| /actors/{actorId}/classify | POST   | Assign actor classification |
| /actors/{actorId}/activate | POST   | Activate actor              |
| /actors/{actorId}/suspend  | POST   | Suspend actor               |

---

# 6.2 Professional Endpoints

| Endpoint                        | Method | Purpose               |
| ------------------------------- | ------ | --------------------- |
| /actors/professionals           | POST   | Register professional |
| /actors/professionals/{actorId} | GET    | Retrieve professional |

---

# 6.3 Patient Endpoints

| Endpoint                   | Method | Purpose          |
| -------------------------- | ------ | ---------------- |
| /actors/patients           | POST   | Register patient |
| /actors/patients/{actorId} | GET    | Retrieve patient |

---

# 7. ORGANIZATION UNIT ENDPOINTS

---

# 7.1 Official Organization Endpoints

| Endpoint                             | Method | Purpose                    |
| ------------------------------------ | ------ | -------------------------- |
| /organization-units                  | POST   | Create organization unit   |
| /organization-units/{unitId}         | GET    | Retrieve organization unit |
| /organization-units/{unitId}         | PUT    | Update organization unit   |
| /organization-units/{unitId}/archive | POST   | Archive organization unit  |

---

# 7.2 Organizational Hierarchy Endpoints

| Endpoint                              | Method | Purpose                     |
| ------------------------------------- | ------ | --------------------------- |
| /organization-units/{unitId}/children | GET    | Retrieve hierarchy children |
| /organization-units/{unitId}/parent   | PUT    | Assign parent unit          |
| /organization-units/hierarchy         | GET    | Retrieve hierarchy tree     |

---

# 8. OWNERSHIP ENDPOINTS

---

# 8.1 Official Ownership Endpoints

| Endpoint                           | Method | Purpose            |
| ---------------------------------- | ------ | ------------------ |
| /ownerships                        | POST   | Assign ownership   |
| /ownerships/{ownershipId}          | GET    | Retrieve ownership |
| /ownerships/{ownershipId}/transfer | POST   | Transfer ownership |
| /ownerships/{ownershipId}/revoke   | POST   | Revoke ownership   |

---

# 8.2 Ownership History Endpoints

| Endpoint                          | Method | Purpose                   |
| --------------------------------- | ------ | ------------------------- |
| /ownerships/{ownershipId}/history | GET    | Retrieve transfer history |

---

# 9. REQUEST DTO CONTRACTS

---

# 9.1 User Registration Request

Recommended structure:

```json id="userregistrationrequest"
{
  "identityId": "identity-001",
  "firstName": "Borys",
  "lastName": "Espinoza",
  "displayName": "Borys Espinoza",
  "birthDate": "1995-01-01",
  "gender": "MALE",
  "phone": "+593999999999",
  "locale": "es-EC",
  "timezone": "America/Guayaquil"
}
```

---

# 9.2 Membership Creation Request

```json id="membershipcreationrequest"
{
  "actorId": "actor-001",
  "organizationUnitId": "branch-001",
  "membershipType": "PRIMARY"
}
```

---

# 9.3 Actor Classification Request

```json id="actorclassificationrequest"
{
  "actorType": "PROFESSIONAL",
  "specialty": "ORTHODONTICS",
  "licenseNumber": "DEN-001245"
}
```

---

# 9.4 Organization Unit Request

```json id="organizationunitrequest"
{
  "name": "North Branch",
  "code": "UIO-NORTH",
  "unitType": "BRANCH",
  "parentUnitId": "hq-001"
}
```

---

# 9.5 Ownership Assignment Request

```json id="ownershipassignmentrequest"
{
  "resourceType": "MEDICAL_RECORD",
  "resourceId": "record-001",
  "ownerActorId": "actor-001",
  "ownershipType": "RESPONSIBLE"
}
```

---

# 9.6 Ownership Transfer Request

```json id="ownershiptransferrequest"
{
  "newOwnerActorId": "actor-002",
  "reason": "REASSIGNMENT"
}
```

---

# 10. RESPONSE DTO CONTRACTS

---

# 10.1 User Profile Response

Recommended structure:

```json id="userprofileresponse"
{
  "userId": "user-001",
  "tenantId": "tenant-001",
  "actorId": "actor-001",
  "displayName": "Borys Espinoza",
  "status": "ACTIVE",
  "createdAt": "2026-05-17T10:00:00Z"
}
```

---

# 10.2 Membership Response

```json id="membershipresponse"
{
  "membershipId": "membership-001",
  "actorId": "actor-001",
  "organizationUnitId": "branch-001",
  "membershipType": "PRIMARY",
  "status": "ACTIVE"
}
```

---

# 10.3 Actor Response

```json id="actorresponse"
{
  "actorId": "actor-001",
  "actorType": "PROFESSIONAL",
  "status": "ACTIVE",
  "specialty": "ORTHODONTICS"
}
```

---

# 10.4 Organization Unit Response

```json id="organizationunitresponse"
{
  "organizationUnitId": "branch-001",
  "name": "North Branch",
  "unitType": "BRANCH",
  "status": "ACTIVE"
}
```

---

# 10.5 Ownership Response

```json id="ownershipresponse"
{
  "ownershipId": "ownership-001",
  "resourceType": "MEDICAL_RECORD",
  "resourceId": "record-001",
  "ownerActorId": "actor-001",
  "ownershipType": "RESPONSIBLE"
}
```

---

# 10.6 Error Response Contract

Recommended structure:

```json id="usererrorresponse"
{
  "timestamp": "2026-05-17T10:00:00Z",
  "correlationId": "corr-001",
  "traceId": "trace-001",
  "errorCode": "MEMBERSHIP_SUSPENDED",
  "message": "Operational participation denied",
  "path": "/api/v1/memberships/membership-001"
}
```

---

# 11. API VALIDATION RULES

---

# 11.1 Request Validation Principle

All User APIs MUST validate:

* tenant ownership
* membership eligibility
* organizational consistency
* ownership validity
* operational lifecycle eligibility

---

# 11.2 Validation Failure Rules

Invalid requests SHOULD return:

```text id="uservalidationstatus"
400 Bad Request
```

---

# 11.3 Operational Restriction Rules

Operationally invalid actors MUST:

* reject protected execution safely

---

# 12. MULTITENANCY RULES

---

# 12.1 Tenant Isolation Principle

User APIs MUST preserve:

* strict tenant isolation

---

# 12.2 Cross Tenant Access Forbidden

User APIs MUST NEVER:

* expose another tenant’s operational state
* mutate another tenant’s organizational structures
* leak ownership relationships

---

# 12.3 Tenant Context Propagation

Tenant metadata MUST propagate through:

* JWT claims
* Reactor Context
* observability metadata
* distributed workflows

---

# 13. SECURITY CONTRACT RULES

---

# 13.1 Secure API Principle

User APIs MUST:

* require HTTPS
* validate tenant ownership
* validate operational eligibility

---

# 13.2 Sensitive Exposure Restrictions

User APIs MUST NEVER expose:

* credentials
* password hashes
* internal aggregate state
* authorization internals

---

# 13.3 Ownership Protection Principle

Ownership APIs MUST:

* preserve ownership traceability
* preserve organizational visibility restrictions

---

# 14. STATUS CODE RULES

---

# 14.1 Recommended Status Codes

| Status Code | Purpose                   |
| ----------- | ------------------------- |
| 200         | Successful operation      |
| 201         | Resource created          |
| 204         | Successful empty response |
| 400         | Validation failure        |
| 401         | Authentication failure    |
| 403         | Authorization failure     |
| 404         | Resource not found        |
| 409         | Concurrency conflict      |

---

# 14.2 Operational Restriction Status

Restricted operational participation SHOULD return:

```text id="userrestrictedstatus"
403 Forbidden
```

---

# 15. REACTIVE CONTRACT RULES

---

# 15.1 Official Reactive Standard

User APIs MUST remain:

* non-blocking
* Reactor-compatible
* async-safe

---

# 15.2 Blocking Operations Forbidden

Forbidden:

* JDBC
* Thread.sleep
* .block()
* imperative waiting

inside API execution chains.

---

# 15.3 Reactive Context Propagation

Reactive APIs MUST preserve:

* tenant context
* actor context
* correlation IDs
* trace IDs
* ownership metadata

---

# 16. OBSERVABILITY RULES

---

# 16.1 Traceability Principle

User APIs MUST expose:

* correlation IDs
* trace IDs
* actor-aware diagnostics

---

# 16.2 Mandatory Metadata

Recommended metadata:

```text id="userapimetadata"
tenant_id
actor_id
membership_id
correlation_id
trace_id
organization_unit
```

---

# 16.3 Organizational Visibility Principle

Membership and ownership APIs SHOULD remain:

* observable
* measurable
* diagnosable

---

# 17. AUDITING RULES

---

# 17.1 Mandatory Auditability

Critical User APIs MUST generate:

* audit records
* operational traces
* ownership history

---

# 17.2 Mandatory Audited Endpoints

The following MUST remain auditable:

* User registration
* Membership creation
* Membership suspension
* Ownership assignment
* Ownership transfer
* Professional registration
* Patient registration
* Organizational restructuring

---

# 18. IDEMPOTENCY RULES

---

# 18.1 Idempotency Principle

Sensitive User operations SHOULD support:

* idempotent execution

---

# 18.2 Retry Safety Principle

Retries MUST preserve:

* organizational consistency
* ownership consistency
* membership integrity

---

# 19. VERSIONING RULES

---

# 19.1 API Versioning Principle

Public User APIs SHOULD support:

* backward compatibility
* explicit versioning

---

# 19.2 Recommended Versioning Strategy

Recommended format:

```text id="userversioningstrategy"
/api/v1/users
```

---

# 19.3 Breaking Changes Rule

Breaking changes MUST:

* increment major version
* preserve migration strategy

---

# 20. FAILURE HANDLING RULES

---

# 20.1 Failure Isolation Principle

API failures SHOULD remain:

* isolated
* observable
* recoverable

---

# 20.2 Organizational Failure Principle

Organizational failures MUST:

* avoid inconsistent hierarchy states

---

# 20.3 Ownership Failure Principle

Ownership failures MUST:

* preserve traceability consistency

---

# 21. FORBIDDEN API ANTI-PATTERNS

---

# Forbidden

* Cross-tenant ownership leakage
* Blocking reactive APIs
* Entity exposure
* Shared mutable organizational state
* Tenant-blind operations
* Hidden operational side effects
* Oversized payloads
* Infrastructure leakage
* Non-traceable failures
* Imperative reactive leakage

---

# 22. AI IMPLEMENTATION RULES

All AI-generated User APIs MUST:

* remain fully reactive
* preserve tenant isolation
* preserve ownership traceability
* preserve membership consistency
* avoid entity exposure
* avoid blocking execution
* preserve observability
* preserve auditability
* preserve reactive context propagation
* support scalable organizational execution

---

# 23. CODECORE USER API PHILOSOPHY

```text id="userapiphilosophy"
User Management APIs exist to expose
reactive, scalable and tenant-safe
human operational participation capabilities
through contextual organizational contracts,
ownership propagation governance
and consistency-preserving operational execution boundaries.
```

```
```
