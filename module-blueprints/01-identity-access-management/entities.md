# entities.md

````md id="s0wq91"
# Identity & Access Management (IAM)
## Entity Design
### CodeCore Module Blueprints
### Version 1.0

---

# 1. PURPOSE

This document defines the official entity model for the Identity & Access Management (IAM) bounded context.

Its objectives are:

- define entity responsibilities
- establish entity ownership
- preserve aggregate consistency
- standardize identity lifecycle structures
- prevent domain leakage
- support reactive-safe persistence
- enforce tenant-aware identity management
- guide AI-assisted development

---

# 2. ENTITY PHILOSOPHY

IAM entities exist to:
- model authentication state
- support security workflows
- preserve identity integrity
- maintain session lifecycle consistency
- enforce authentication boundaries

IAM entities MUST:
- belong to aggregate boundaries
- remain tenant-aware
- preserve identity consistency
- avoid business workflow orchestration

---

# 3. OFFICIAL IAM ENTITIES

The IAM bounded context officially defines:

| Entity | Aggregate | Responsibility |
|---|---|---|
| Identity | IdentityAggregate | Authentication identity root |
| Credential | IdentityAggregate | Credential lifecycle |
| Session | SessionAggregate | Active authentication session |
| RefreshToken | SessionAggregate | Token renewal lifecycle |
| PasswordResetRequest | PasswordResetAggregate | Password recovery lifecycle |
| LoginAttemptTracker | LoginAttemptAggregate | Brute-force protection |
| FailedLoginAttempt | LoginAttemptAggregate | Failed login registration |

---

# 4. IDENTITY ENTITY

---

# 4.1 Entity Role

Identity is the Aggregate Root of:
- IdentityAggregate

---

# 4.2 Responsibilities

Identity owns:

- authentication identity
- authentication state
- account lifecycle
- credential association
- lockout state
- tenant ownership

---

# 4.3 Core Attributes

Recommended attributes:

```text id="iamidentityattrs"
id
tenant_id
email
username
status
credential_id
locked
enabled
last_login_at
failed_attempt_count
created_at
updated_at
version
````

---

# 4.4 Lifecycle States

Identity MAY support:

```text id="identitystates"
ACTIVE
LOCKED
DISABLED
PENDING_VERIFICATION
PASSWORD_RESET_REQUIRED
```

---

# 4.5 Behavioral Responsibilities

Identity MAY:

* authenticate
* lock
* unlock
* disable
* enable
* markLastLogin
* requirePasswordReset

---

# 4.6 Forbidden Responsibilities

Identity MUST NOT:

* manage permissions
* manage business profiles
* manage tenant lifecycle
* manage notifications
* orchestrate workflows

---

# 4.7 Tenant Rules

Identity MUST:

* belong to one tenant
* preserve immutable tenant ownership

---

# 5. CREDENTIAL ENTITY

---

# 5.1 Entity Role

Credential belongs to:

* IdentityAggregate

---

# 5.2 Responsibilities

Credential owns:

* password hash
* credential expiration
* password rotation lifecycle
* password history metadata

---

# 5.3 Core Attributes

Recommended attributes:

```text id="credentialattrs"
id
identity_id
password_hash
password_changed_at
password_expires_at
must_change_password
created_at
updated_at
version
```

---

# 5.4 Security Rules

Credential MUST:

* store only hashed passwords
* never expose raw credentials
* remain inaccessible externally

---

# 5.5 Behavioral Responsibilities

Credential MAY:

* validateRotationEligibility
* rotatePassword
* expirePassword
* requirePasswordReset

---

# 5.6 Forbidden Responsibilities

Credential MUST NOT:

* authenticate sessions
* issue JWT tokens
* manage permissions

---

# 6. SESSION ENTITY

---

# 6.1 Entity Role

Session is the Aggregate Root of:

* SessionAggregate

---

# 6.2 Responsibilities

Session owns:

* session lifecycle
* refresh lifecycle
* revocation state
* device association
* expiration lifecycle

---

# 6.3 Core Attributes

Recommended attributes:

```text id="sessionattrs"
id
tenant_id
identity_id
status
device_id
ip_address
user_agent
last_activity_at
expires_at
revoked_at
created_at
updated_at
version
```

---

# 6.4 Session States

Session MAY support:

```text id="sessionstates"
ACTIVE
REVOKED
EXPIRED
INVALIDATED
```

---

# 6.5 Behavioral Responsibilities

Session MAY:

* revoke
* expire
* validateRefreshEligibility
* registerActivity

---

# 6.6 Forbidden Responsibilities

Session MUST NOT:

* authenticate passwords
* manage permissions
* manage business data

---

# 7. REFRESH TOKEN ENTITY

---

# 7.1 Entity Role

RefreshToken belongs to:

* SessionAggregate

---

# 7.2 Responsibilities

RefreshToken owns:

* refresh lifecycle
* rotation lifecycle
* expiration lifecycle
* revocation integrity

---

# 7.3 Core Attributes

Recommended attributes:

```text id="refreshtokenattrs"
id
session_id
token_hash
issued_at
expires_at
revoked_at
rotated_from
created_at
updated_at
version
```

---

# 7.4 Security Rules

RefreshToken MUST:

* remain hashed if persisted
* remain single-owner
* support revocation

---

# 7.5 Behavioral Responsibilities

RefreshToken MAY:

* revoke
* rotate
* validateExpiration
* validateUsageEligibility

---

# 8. PASSWORD RESET REQUEST ENTITY

---

# 8.1 Entity Role

PasswordResetRequest is the Aggregate Root of:

* PasswordResetAggregate

---

# 8.2 Responsibilities

PasswordResetRequest owns:

* reset lifecycle
* reset expiration
* reset validation
* reset completion state

---

# 8.3 Core Attributes

Recommended attributes:

```text id="passwordresetattrs"
id
tenant_id
identity_id
reset_token_hash
expires_at
used_at
status
created_at
updated_at
version
```

---

# 8.4 Reset States

Password reset requests MAY support:

```text id="passwordresetstates"
PENDING
USED
EXPIRED
CANCELLED
```

---

# 8.5 Behavioral Responsibilities

PasswordResetRequest MAY:

* expire
* markUsed
* validateEligibility
* invalidate

---

# 9. LOGIN ATTEMPT TRACKER ENTITY

---

# 9.1 Entity Role

LoginAttemptTracker is the Aggregate Root of:

* LoginAttemptAggregate

---

# 9.2 Responsibilities

LoginAttemptTracker owns:

* failed attempt counting
* attempt windows
* brute-force protection
* temporary restrictions

---

# 9.3 Core Attributes

Recommended attributes:

```text id="logintrackerattrs"
id
tenant_id
identity_id
failed_attempts
last_failed_at
locked_until
risk_score
created_at
updated_at
version
```

---

# 9.4 Behavioral Responsibilities

LoginAttemptTracker MAY:

* registerFailure
* registerSuccess
* evaluateRisk
* triggerTemporaryLock
* clearAttempts

---

# 10. FAILED LOGIN ATTEMPT ENTITY

---

# 10.1 Entity Role

FailedLoginAttempt belongs to:

* LoginAttemptAggregate

---

# 10.2 Responsibilities

FailedLoginAttempt stores:

* authentication failure traceability
* attempt metadata
* suspicious activity information

---

# 10.3 Core Attributes

Recommended attributes:

```text id="failedloginattrs"
id
tenant_id
identity_id
ip_address
user_agent
failure_reason
attempted_at
correlation_id
trace_id
created_at
```

---

# 10.4 Behavioral Responsibilities

FailedLoginAttempt SHOULD remain:

* immutable
* append-only

---

# 11. ENTITY RELATIONSHIP RULES

---

# 11.1 Aggregate Boundary Principle

Entities MUST remain inside:

* aggregate consistency boundaries

---

# 11.2 Cross Aggregate Entity References

Entities SHOULD reference external aggregates ONLY through:

* identifiers

---

# 11.3 Direct Cross Aggregate Mutation Forbidden

Entities MUST NOT:

* mutate external aggregate internals

---

# 12. MULTITENANCY RULES

---

# 12.1 Tenant Ownership Principle

All tenant-owned entities MUST contain:

```text id="tenantownership"
tenant_id
```

---

# 12.2 Cross Tenant References Forbidden

Entities MUST NEVER:

* reference entities from another tenant

---

# 12.3 Tenant Ownership Immutability

tenant_id MUST remain:

* immutable

after creation.

---

# 13. REACTIVE RULES

---

# 13.1 Reactive Persistence Principle

IAM entities MUST support:

* non-blocking persistence
* Reactor-compatible execution

---

# 13.2 Blocking Logic Forbidden

Entities MUST NOT:

* perform blocking I/O
* invoke external APIs

---

# 14. SECURITY RULES

---

# 14.1 Sensitive Data Protection

Entities MUST protect:

* password hashes
* refresh tokens
* reset tokens
* security metadata

---

# 14.2 Serialization Restrictions

Sensitive entities MUST NOT:

* serialize confidential fields unintentionally

---

# 14.3 Exposure Restrictions

Credential-related entities MUST NEVER:

* leave domain boundaries directly

---

# 15. AUDITING RULES

---

# 15.1 Security Auditability

Critical entity transitions SHOULD remain:

* auditable
* traceable
* tenant-aware

---

# 15.2 Immutable Security History

Security-sensitive history SHOULD remain:

* append-only when possible

---

# 16. CONCURRENCY RULES

---

# 16.1 Optimistic Locking Principle

Critical IAM entities SHOULD support:

* optimistic locking

---

# 16.2 Concurrent Authentication Protection

Concurrent authentication operations MUST:

* preserve state consistency

---

# 16.3 Refresh Token Concurrency Protection

Refresh token rotation MUST remain:

* concurrency-safe

---

# 17. OBSERVABILITY RULES

---

# 17.1 Traceability Principle

Critical entity operations SHOULD expose:

* correlation IDs
* trace IDs
* tenant traceability

---

# 17.2 Security Visibility

Authentication anomalies SHOULD remain:

* observable
* measurable

---

# 18. FORBIDDEN ENTITY ANTI-PATTERNS

---

# Forbidden

* Anemic security entities
* Mutable credential exposure
* Cross-aggregate direct references
* Tenant-blind entities
* Shared mutable security state
* Workflow orchestration inside entities
* Blocking infrastructure calls
* Permission ownership inside IAM entities
* Raw token persistence
* Plain text password handling

---

# 19. AI IMPLEMENTATION RULES

All AI-generated IAM entities MUST:

* preserve aggregate boundaries
* preserve tenant isolation
* avoid raw credential exposure
* support optimistic locking
* remain reactive-safe
* avoid blocking logic
* preserve immutable security history
* avoid cross-aggregate mutation
* preserve serialization safety
* preserve security consistency

---

# 20. CODECORE IAM ENTITY PHILOSOPHY

```text id="entityphilosophy"
IAM entities exist to preserve
authentication integrity,
credential safety,
session consistency
and tenant-aware identity security
through isolated reactive domain behavior.
```

```
```
