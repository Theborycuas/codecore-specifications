# 05-authentication-management/security-rules.md

> **DEPRECATED** — See [DEPRECATED.md](./DEPRECATED.md). Authoritative: [IAM](../01-identity-access-management/).

````md id="t4x8vp"
# Authentication Management Security Rules

## 1. Introduction

This document defines the security rules enforced by the Authentication Management module.

These rules establish the foundational security protections required to secure:

- User identities
- Authentication flows
- Sessions
- Tokens
- MFA processes
- Device trust
- Internal service authentication
- Distributed authentication ecosystems

The rules are designed following:

- Zero Trust Architecture
- Defense in Depth
- Secure-by-default principles
- OWASP ASVS recommendations
- NIST authentication guidance
- Enterprise SaaS security standards

---

# 2. Security Principles

## 2.1 Deny by Default

If authentication cannot be validated:

```text id="m7v2wr"
AUTHENTICATION = DENIED
````

---

## 2.2 Zero Trust Authentication

Every request must independently validate:

* Identity
* Session
* Token integrity
* Tenant context
* Device trust
* Security policies

---

## 2.3 Least Privilege

Authentication sessions should expose minimal scope and lifetime.

---

## 2.4 Immutable Auditability

Critical authentication actions must remain traceable.

---

## 2.5 Short-Lived Access

Access tokens should minimize exposure windows.

---

# 3. Credential Security Rules

## 3.1 Plaintext Password Storage Forbidden

Passwords must never be:

* Stored plaintext
* Logged
* Published in events
* Cached insecurely

---

## 3.2 Approved Hashing Algorithms Only

Recommended algorithms:

```text id="p3n8vx"
Argon2
bcrypt
PBKDF2
```

---

## 3.3 Salting Mandatory

All password hashes require secure salts.

---

## 3.4 Password Complexity Enforcement

Recommended minimums:

```text id="r6w1tp"
- Minimum 12 characters
- Uppercase
- Lowercase
- Numeric
- Special characters
```

---

## 3.5 Breached Password Detection

Recommended integration with:

* HaveIBeenPwned
* Internal breach lists

---

## 3.6 Password Rotation Policies

Optional based on compliance requirements.

---

# 4. Tenant Isolation Rules

## 4.1 Cross-Tenant Authentication Forbidden

Default behavior:

```text id="y2k7wr"
Tenant A users
cannot authenticate
into Tenant B
```

---

## 4.2 Mandatory Tenant Context

All authentication operations require:

```text id="u8v4xp"
tenantId
```

---

## 4.3 Tenant-Aware Sessions

Sessions must remain tenant-scoped.

---

## 4.4 Tenant-Aware JWT Claims

JWTs must include tenant context.

---

# 5. JWT Security Rules

## 5.1 JWT Signature Validation Mandatory

Unsigned tokens forbidden.

---

## 5.2 JWT Expiration Mandatory

Expired tokens:

```text id="q4m9wt"
DENY ACCESS
```

---

## 5.3 Short-Lived Access Tokens

Recommended:

| Token         | Recommended Lifetime |
| ------------- | -------------------- |
| Access Token  | 5–30 minutes         |
| MFA Challenge | 5 minutes            |
| Reset Token   | 15–30 minutes        |

---

## 5.4 Sensitive Claims Forbidden

JWTs must never contain:

* Passwords
* Secrets
* Internal hashes
* Sensitive medical information

---

## 5.5 JWT Must Not Be Trusted Alone

Authorization still requires:

* Permission validation
* Policy evaluation
* Session validation

---

## 5.6 Audience Validation Recommended

Validate:

```text id="k7x3vp"
aud
```

to prevent misuse.

---

# 6. Refresh Token Security Rules

## 6.1 Rotation Mandatory

Refresh tokens must rotate after use.

---

## 6.2 Replay Detection Mandatory

Reuse detection must trigger:

```text id="g5v8wr"
- Session revocation
- Security alerts
- Re-authentication
```

---

## 6.3 Refresh Tokens Stored Hashed

Raw token persistence forbidden.

---

## 6.4 Revocation Support Mandatory

Compromised tokens require immediate invalidation.

---

## 6.5 Long-Lived Tokens Require Additional Controls

Examples:

* Device binding
* IP heuristics
* Step-up MFA

---

# 7. Session Security Rules

## 7.1 Session Revocation Support Mandatory

Sessions must support immediate invalidation.

---

## 7.2 Revoked Sessions Invalid

Validation result:

```text id="n1w6xt"
DENY
```

---

## 7.3 Session Expiration Mandatory

Inactive sessions must expire automatically.

---

## 7.4 Concurrent Session Restrictions

Optional policies may limit:

* Maximum active sessions
* Device count
* Geographic usage

---

## 7.5 Session Ownership Immutable

Sessions cannot transfer between users.

---

# 8. MFA Security Rules

## 8.1 MFA Required for High-Risk Operations

Recommended examples:

```text id="v9m2wr"
- Password changes
- Privilege escalation
- Sensitive data access
```

---

## 8.2 MFA Challenge Expiration Mandatory

Challenges must expire rapidly.

---

## 8.3 MFA Replay Forbidden

Verification codes must be single-use.

---

## 8.4 MFA Brute Force Protection

Excessive invalid attempts require:

* Temporary lockout
* Risk escalation
* Additional verification

---

## 8.5 MFA Secrets Protection

TOTP secrets must be encrypted at rest.

---

# 9. Device Trust Security Rules

## 9.1 Trusted Devices Are Not Absolute Trust

Trusted devices reduce friction but do not bypass security entirely.

---

## 9.2 Device Revocation Support Mandatory

Compromised devices require immediate revocation.

---

## 9.3 Device Fingerprint Protection

Device metadata should avoid privacy violations.

---

## 9.4 Suspicious Device Detection

Examples:

```text id="f6x1vp"
- New country
- Browser changes
- Impossible travel
```

---

# 10. Account Protection Rules

## 10.1 Brute Force Protection Mandatory

Required protections:

* Rate limiting
* Progressive delays
* Lockout thresholds

---

## 10.2 Account Enumeration Prevention

Authentication responses should avoid revealing:

```text id="w3n7xt"
- User existence
- Account status
```

---

## 10.3 Account Lockout Policies

Recommended after repeated failures.

---

## 10.4 Password Reset Protection

Password reset flows require:

* Expiring tokens
* Single-use tokens
* Ownership validation

---

# 11. API Security Rules

## 11.1 HTTPS Mandatory

Authentication APIs must never use plaintext transport.

---

## 11.2 Internal APIs Require Strong Authentication

Recommended:

* mTLS
* Signed JWT
* Service identity validation

---

## 11.3 Rate Limiting Mandatory

Critical endpoints:

| Endpoint       | Recommendation |
| -------------- | -------------- |
| Login          | Strict         |
| MFA            | Strict         |
| Password reset | Strict         |
| Token refresh  | Medium         |

---

## 11.4 Correlation IDs Required

Security observability requires:

```text id="j8v4wp"
X-Correlation-ID
```

---

## 11.5 Secure Headers Recommended

| Header           | Purpose          |
| ---------------- | ---------------- |
| Authorization    | Identity         |
| X-Tenant-ID      | Tenant isolation |
| X-Correlation-ID | Tracing          |

---

# 12. OAuth2/OIDC Security Rules

## 12.1 State Validation Mandatory

Prevents CSRF attacks.

---

## 12.2 Provider Signature Validation Mandatory

Identity providers must be verified.

---

## 12.3 Redirect URI Validation Mandatory

Open redirects forbidden.

---

## 12.4 External Identity Mapping Validation

Prevent unauthorized account linking.

---

# 13. Service Authentication Rules

## 13.1 Internal Services Must Authenticate

No implicit trust between services.

---

## 13.2 mTLS Strongly Recommended

Especially for:

* Internal APIs
* Critical services
* Cross-region traffic

---

## 13.3 Service Credentials Must Rotate

Static long-term secrets discouraged.

---

## 13.4 Service Identity Isolation

Each service requires unique identity.

---

# 14. Audit Security Rules

## 14.1 Mandatory Authentication Auditing

Audit required for:

| Action             | Required |
| ------------------ | -------- |
| Login success      | Yes      |
| Login failure      | Yes      |
| MFA validation     | Yes      |
| Password reset     | Yes      |
| Session revocation | Yes      |

---

## 14.2 Audit Immutability

Audit evidence must not be mutable.

---

## 14.3 Sensitive Data Restrictions

Audit logs must never contain:

* Passwords
* Secrets
* Raw JWTs
* MFA secrets

---

# 15. Cache Security Rules

## 15.1 Session Cache Isolation

Authentication caches must be tenant-aware.

---

## 15.2 Cache Invalidation Mandatory

Required after:

```text id="z5m8vr"
- Logout
- Password reset
- Session revocation
- Token replay detection
```

---

## 15.3 JWT Blacklist Synchronization

Revoked tokens require synchronization.

---

# 16. Reactive Security Rules

## 16.1 Immutable Security Context

Reactive pipelines require immutable context propagation.

---

## 16.2 Context Leakage Prevention

Tenant/security contexts must not leak across reactive chains.

---

## 16.3 Non-Blocking Security Validation

Blocking authentication logic discouraged.

---

# 17. Distributed System Security Rules

## 17.1 Stateless Authentication Preferred

JWT preferred for scalability.

---

## 17.2 Clock Synchronization Mandatory

Required for:

* JWT expiration
* MFA expiration
* Session expiration

---

## 17.3 Distributed Revocation Support

Revocations must propagate rapidly.

---

## 17.4 Secure Inter-Service Communication

Recommended:

```text id="x2k9wt"
TLS everywhere
mTLS internally
```

---

# 18. Monitoring and Threat Detection Rules

## 18.1 Suspicious Activity Monitoring

Monitor:

```text id="g7v3xp"
- Excessive login failures
- Token replay
- Impossible travel
- Suspicious devices
- MFA abuse
```

---

## 18.2 High-Severity Security Alerts

Examples:

```text id="u4m8wr"
- Refresh token replay
- Session hijacking
- OAuth tampering
```

---

## 18.3 Security Incident Persistence

Incidents must remain auditable.

---

# 19. Failure Handling Rules

## 19.1 Fail Closed Principle

Unexpected failures:

```text id="q1x6vt"
AUTHENTICATION = DENIED
```

---

## 19.2 Cache Failure Handling

Fallback strategies required.

---

## 19.3 Replay Uncertainty Handling

Uncertain replay:

```text id="n8v2wr"
REVOKE SESSION
```

---

# 20. Compliance Considerations

The module should support:

* GDPR
* HIPAA
* SOC2
* ISO 27001
* OWASP ASVS
* NIST authentication standards

depending on business requirements.

---

# 21. OWASP Security Alignment

The module mitigates:

| OWASP Risk                | Mitigation      |
| ------------------------- | --------------- |
| Broken Authentication     | MFA + rotation  |
| Credential Stuffing       | Rate limiting   |
| Session Hijacking         | Revocation      |
| Replay Attacks            | Rotation        |
| Security Misconfiguration | Secure defaults |

---

# 22. Operational Security Recommendations

Recommended practices:

| Practice            | Recommendation |
| ------------------- | -------------- |
| Penetration testing | Periodic       |
| Dependency scanning | Continuous     |
| Secret rotation     | Automated      |
| Audit reviews       | Continuous     |
| MFA adoption        | Encouraged     |

---

# 23. Future Security Extensions

Future security enhancements may include:

* Passwordless authentication
* WebAuthn
* Continuous authentication
* Adaptive authentication
* Behavioral biometrics
* Risk-based authentication
* Hardware security keys

---

# 24. Security Checklist

## Mandatory Controls

| Control                | Required |
| ---------------------- | -------- |
| Password hashing       | Yes      |
| JWT validation         | Yes      |
| Session revocation     | Yes      |
| Refresh token rotation | Yes      |
| MFA support            | Yes      |
| Tenant isolation       | Yes      |
| Audit logging          | Yes      |
| Replay detection       | Yes      |

---

# 25. Summary

The Authentication Management security rules provide:

* Enterprise-grade authentication protection
* Strong credential security
* Replay-resistant token management
* MFA enforcement
* Distributed authentication consistency
* Zero Trust authentication foundations
* Multi-tenant isolation
* Immutable authentication auditability

These rules establish the security baseline of the authentication ecosystem.

```
```
