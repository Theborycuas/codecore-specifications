# Security Context Propagation

## CodeCore Engineering Specifications

### Version 1.0

---

# 1. PURPOSE

This document defines the official Security Context Propagation Rules for CodeCore.

Its objectives are:

* standardize reactive security propagation
* preserve authentication integrity
* enforce authorization boundaries
* prevent security context leakage
* protect tenant-aware access control
* ensure non-blocking security execution
* guide AI-assisted development
* maintain scalable reactive security consistency

This specification is mandatory for:

* authentication flows
* authorization systems
* JWT propagation
* reactive pipelines
* event processing
* multitenancy enforcement
* service execution
* AI-generated implementations

---

# 2. SECURITY PHILOSOPHY

---

## 2.1 Official Definition

Security Context is:

```text id="1sec1"
The authenticated operational identity
and authorization metadata propagated
through the execution lifecycle.
```

---

## 2.2 Core Principle

Security propagation exists to preserve:

* authentication integrity
* authorization consistency
* tenant-safe access control
* traceable execution identity

---

## 2.3 Reactive Security Principle

Security propagation MUST remain:

* reactive
* non-blocking
* context-safe
* asynchronous

---

# 3. OFFICIAL SECURITY CONTEXT STANDARD

---

# 3.1 Official Propagation Mechanism

CodeCore officially adopts:

```text id="2sec2"
Reactive Security Context + Reactor Context
```

---

# 3.2 ThreadLocal Forbidden

ThreadLocal-based security propagation is forbidden inside reactive flows.

---

## Forbidden

```text id="3sec3"
SecurityContextHolder.getContext()
Static authentication holders
Thread-bound authentication
```

inside reactive execution chains.

---

# 3.3 Mandatory Context Preservation

Reactive pipelines MUST preserve:

* authentication
* authorization
* tenant identity
* traceability metadata

---

# 4. AUTHENTICATION RULES

---

# 4.1 Official Authentication Strategy

CodeCore officially adopts:

```text id="4sec4"
JWT + Refresh Token
```

---

# 4.2 Stateless Authentication Principle

Authentication SHOULD remain:

* stateless
* token-based
* scalable

---

# 4.3 JWT Ownership Principle

JWT tokens are issued by **Identity & Access Management (IAM)** and represent:

* authenticated identity
* tenant-scoped execution context
* session binding
* **coarse** authorization hints (not definitive authorization)

JWTs MUST NOT be treated as the sole source of authorization truth.

---

# 4.4 Hybrid JWT Claim Strategy (AUD-003)

CodeCore adopts a **hybrid JWT model**:

* JWT carries identity and coarse access context for scalability
* **Authorization Management** remains the authoritative runtime validator for permissions and dynamic policies

### 4.4.1 Allowed JWT Claims

Recommended claims:

```text id="5sec5"
sub
tenant_id
session_id
roles          # coarse role identifiers only
scopes         # coarse scope identifiers only
iat
exp
jti
```

### 4.4.2 Coarse vs sensitive authorization

| Claim type | JWT may carry | Authoritative validation |
|------------|---------------|-------------------------|
| Identity (`sub`, `tenant_id`, `session_id`) | Yes | IAM + gateway introspection |
| Coarse roles | Yes (hints) | Authorization Management |
| Coarse scopes | Yes (hints) | Authorization Management |
| Fine-grained permissions | Discouraged | Authorization Management only |
| Dynamic policies (resource, context) | No | Authorization Management only |

### 4.4.3 JWT is not definitive authorization

The following MUST be enforced in documentation and implementation:

* **Sensitive permissions MUST NOT depend solely on JWT claims.**
* **JWT claims are not definitive authorization.**
* **Authorization Management has priority** over token hints for protected operations.
* Gateway or edge caches MAY use coarse claims for routing only, not for final deny/allow on sensitive resources.

---

# 4.5 Sensitive Claim Restrictions

JWTs MUST NOT expose:

* passwords
* secrets
* sensitive business data
* exhaustive permission matrices
* clinical or financial record payloads

---

# 5. AUTHORIZATION RULES

---

# 5.1 Official Authorization Strategy

CodeCore officially adopts:

```text id="6sec6"
RBAC + Permission-Based Authorization
```

---

# 5.2 Authorization Principle

**Authorization Management** is the **authoritative** source for:

* permission evaluation
* dynamic policy execution
* resource-level authorization
* tenant boundary enforcement (in coordination with Tenant context)

Authorization MUST validate:

* tenant ownership
* role permissions
* resource visibility
* operation eligibility

JWT coarse roles/scopes MAY accelerate read-only or low-risk paths but MUST NOT replace Authorization Management for sensitive operations.

---

# 5.3 Default Deny Principle

Access MUST be denied by default unless:

* explicitly authorized

---

# 5.4 Permission Granularity Principle

Permissions SHOULD remain:

* explicit
* operation-oriented
* bounded-context-safe

---

## Examples

```text id="7sec7"
APPOINTMENT_CREATE
USER_UPDATE
FORM_SUBMIT
TENANT_VIEW
```

---

# 6. REACTIVE SECURITY PROPAGATION RULES

---

# 6.1 Reactor Context Propagation

Security metadata MUST propagate through:

* Reactor Context

---

# 6.2 Context Loss Forbidden

Reactive chains MUST NEVER lose:

* authentication
* authorization metadata
* tenant context

---

# 6.3 Async Security Safety

Asynchronous processing MUST preserve:

* security integrity
* authenticated identity

---

# 6.4 Reactive Context Access

Services SHOULD access security information through:

* Reactive Security Context
* Reactor Context

NOT static holders.

---

# 7. SERVICE SECURITY RULES

---

# 7.1 Security Validation Principle

Services MUST validate:

* authorization
* tenant ownership
* permission scope

before mutation.

---

# 7.2 Service Isolation Principle

Services MUST NOT trust:

* frontend authorization assumptions

---

# 7.3 Explicit Security Boundaries

Security-sensitive operations MUST:

* validate identity explicitly
* validate permissions explicitly

---

# 7.4 Security-Oriented Orchestration

Workflow orchestration MUST preserve:

* authenticated execution context

---

# 8. EVENT SECURITY RULES

---

# 8.1 Event Security Principle

Events MUST preserve:

* tenant-safe propagation
* traceability
* authorization-safe visibility

---

# 8.2 Sensitive Event Restrictions

Events MUST NOT expose:

* secrets
* authentication credentials
* security-sensitive payloads

---

# 8.3 Event Consumer Authorization

Critical event consumers SHOULD validate:

* authorization context
* tenant ownership

when applicable.

---

# 9. MULTITENANCY SECURITY RULES

---

# 9.1 Tenant-Aware Security Principle

Authentication and authorization MUST remain:

* tenant-aware

---

# 9.2 Cross Tenant Authorization Forbidden

Users MUST NEVER:

* access resources from another tenant

without explicit authorization.

---

# 9.3 Tenant-Bound Identity Principle

Authenticated identities SHOULD remain:

* tenant-scoped

---

# 9.4 Tenant Isolation Enforcement

Security systems MUST enforce:

* tenant visibility boundaries

at all layers.

---

# 10. SESSION RULES

---

# 10.1 Session Philosophy

Sessions SHOULD remain:

* lightweight
* scalable
* revocable

---

# 10.2 Refresh Token Principle

Refresh tokens SHOULD support:

* revocation
* expiration
* rotation

---

# 10.3 Concurrent Session Policy

Concurrent session behavior MUST be:

* explicitly defined
* observable
* revocable

---

# 10.4 Session Revocation Principle

Security-sensitive events SHOULD support:

* forced session invalidation

---

# 11. PASSWORD RULES

---

# 11.1 Password Storage Principle

Passwords MUST:

* be hashed
* never be stored in plain text

---

# 11.2 Official Hashing Strategy

Preferred hashing algorithm:

```text id="8sec8"
BCrypt
```

---

# 11.3 Password Exposure Forbidden

Passwords MUST NEVER:

* appear in logs
* appear in events
* appear in traces

---

# 11.4 Credential Protection Principle

Credential handling MUST remain:

* isolated
* trace-safe
* observable

---

# 12. REACTIVE SECURITY EXECUTION RULES

---

# 12.1 Non Blocking Principle

Security operations MUST remain:

* non-blocking
* reactive-friendly

---

# 12.2 Blocking Security APIs Forbidden

Blocking authentication or authorization calls are forbidden inside reactive flows.

---

# 12.3 Security Context Scalability

Security propagation MUST support:

* horizontal scaling
* distributed execution
* asynchronous workflows

---

# 13. AUDITABILITY RULES

---

# 13.1 Security Traceability

Security-sensitive operations MUST be:

* auditable
* traceable
* tenant-aware

---

# 13.2 Authentication Audit Events

Authentication flows SHOULD generate:

* audit events
* security logs
* trace metadata

---

# 13.3 Sensitive Operation Auditing

Critical actions SHOULD be audited.

---

## Examples

```text id="9sec9"
Role changes
Permission grants
Password resets
Sensitive record access
```

---

# 14. OBSERVABILITY RULES

---

# 14.1 Security Observability

Security flows SHOULD expose:

* authentication metrics
* authorization failures
* suspicious activity indicators

---

# 14.2 Correlation Principle

Security execution MUST preserve:

* correlation IDs
* trace IDs
* tenant traceability

---

# 14.3 Sensitive Logging Restrictions

Security logs MUST avoid:

* sensitive payload exposure

---

# 15. FAILURE HANDLING RULES

---

# 15.1 Fail Secure Principle

Security failures MUST:

* fail safely
* deny access by default

---

# 15.2 Invalid Token Principle

Invalid or expired tokens MUST:

* terminate execution safely

---

# 15.3 Unauthorized Access Handling

Unauthorized access MUST:

* remain observable
* remain auditable

---

# 16. FUTURE SECURITY EXTENSIBILITY

---

# 16.1 Extensibility Principle

Security architecture MUST remain extensible for:

* MFA
* OAuth2
* SSO
* device management
* biometric authentication
* external identity providers

---

# 16.2 Identity Federation Readiness

Security architecture SHOULD remain:

* provider-agnostic
* extensible
* modular

---

# 17. FORBIDDEN SECURITY ANTI-PATTERNS

---

# Forbidden

* ThreadLocal security propagation
* SecurityContextHolder usage inside reactive chains
* Cross-tenant authorization leakage
* Plain text passwords
* JWT secret exposure
* Frontend-only authorization
* Mutable authentication state
* Blocking security validation
* Hardcoded credentials
* Security-sensitive logging
* Trusting client-side permissions

---

# 18. AI IMPLEMENTATION RULES

All AI-generated security logic MUST:

* preserve Reactor Context
* preserve tenant isolation
* avoid ThreadLocal propagation
* remain fully reactive
* avoid blocking security operations
* preserve authorization boundaries
* avoid sensitive logging
* support auditability
* preserve JWT integrity
* fail securely by default

---

# 19. SECURITY DESIGN CHECKLIST

Before implementing security logic verify:

* Is authentication reactive-safe?
* Is Reactor Context preserved?
* Is tenant isolation enforced?
* Is authorization explicit?
* Is access denied by default?
* Are JWT claims safe?
* Are passwords protected?
* Are events security-safe?
* Are logs sensitive-safe?
* Is observability preserved?
* Are sessions revocable?
* Is context propagation non-blocking?
* Is cross-tenant access impossible?
* Is auditability preserved?
* Is future extensibility supported?

---

# 20. CODECORE OFFICIAL SECURITY PHILOSOPHY

```text id="10sec10"
Security exists to preserve authenticated,
tenant-safe and traceable execution
through reactive context propagation,
explicit authorization boundaries
and scalable stateless protection mechanisms.
```
