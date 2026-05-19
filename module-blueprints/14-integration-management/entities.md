# 11-payment-management/entities.md

````md id="o9x4vp"
# Payment Management Entities

## 1. Introduction

This document defines the entities of the Payment Management module.

Entities represent payment domain objects that:

- Possess transactional identity
- Maintain payment lifecycle continuity
- Preserve financial traceability
- Coordinate provider synchronization
- Enable reconciliation
- Support refund execution
- Protect PCI boundaries
- Enforce multi-tenant isolation

The entities are designed following:

- Domain-Driven Design (DDD)
- PCI DSS boundary isolation
- Multi-tenant SaaS governance
- Event-driven payment orchestration
- Reactive transaction processing
- Enterprise financial resilience

---

# 2. Entity Overview

| Entity | Purpose |
|---|---|
| PaymentTransaction | Core payment lifecycle |
| PaymentMethod | Tokenized payment instrument |
| ProviderTransaction | External provider state |
| RefundExecution | Provider reimbursement |
| WebhookEvent | External synchronization |
| PaymentRetry | Retry orchestration |
| FraudAssessment | Fraud evaluation |
| PaymentReconciliation | Consistency validation |
| ProviderRouting | Dynamic provider routing |
| PaymentProjection | CQRS read projection |
| PaymentAuthorization | Authorization metadata |
| PaymentCapture | Capture metadata |
| PaymentFailure | Failure traceability |
| PaymentSession | Checkout/payment session |
| ProviderCredentialReference | External provider linkage |
| WebhookSignature | Webhook verification |
| PaymentAuditRecord | Immutable payment traceability |
| PaymentNotification | Payment communication |
| PaymentDispute | Chargeback/dispute handling |
| PaymentSettlement | Settlement reconciliation |

---

# 3. PaymentTransaction Entity

## Purpose

Represents the core lifecycle of a financial transaction.

---

## Identity

```text id="u5m1wr"
paymentTransactionId
````

---

## Attributes

| Attribute     | Type    | Description                   |
| ------------- | ------- | ----------------------------- |
| transactionId | UUID    | Unique transaction identifier |
| tenantId      | UUID    | Tenant owner                  |
| invoiceId     | UUID    | Related invoice               |
| provider      | String  | Payment provider              |
| status        | String  | Payment lifecycle             |
| amount        | Decimal | Transaction amount            |
| currency      | String  | Transaction currency          |
| createdAt     | Instant | Creation timestamp            |

---

## Lifecycle States

```text id="m8v3xp"
PENDING
AUTHORIZED
CAPTURED
FAILED
REFUNDED
CANCELED
EXPIRED
```

---

## Behaviors

| Behavior           | Description              |
| ------------------ | ------------------------ |
| authorizePayment() | Provider authorization   |
| capturePayment()   | Funds capture            |
| failPayment()      | Failure registration     |
| cancelPayment()    | Transaction cancellation |

---

## Critical Rules

| Rule                          | Description    |
| ----------------------------- | -------------- |
| Duplicate captures forbidden  | Idempotency    |
| Tenant ownership mandatory    | Isolation      |
| Provider correlation required | Reconciliation |

---

# 4. PaymentMethod Entity

## Purpose

Represents tokenized payment instruments.

---

## Identity

```text id="f2x7wr"
paymentMethodId
```

---

## Examples

```text id="r4m9vt"
- Stripe token
- PayPal billing agreement
- Vault reference
```

---

## Attributes

| Attribute       | Type    | Description         |
| --------------- | ------- | ------------------- |
| providerToken   | String  | Tokenized reference |
| maskedNumber    | String  | Masked card         |
| expirationMonth | Integer | Expiration month    |
| expirationYear  | Integer | Expiration year     |

---

## Important Principle

```text id="x9v1wr"
Raw PCI-sensitive data
must never be stored
```

---

## Behaviors

| Behavior             | Description            |
| -------------------- | ---------------------- |
| validateExpiration() | Expiration validation  |
| deactivateMethod()   | Disable payment method |

---

# 5. ProviderTransaction Entity

## Purpose

Represents external provider transaction synchronization.

---

## Identity

```text id="k3m8xp"
providerTransactionId
```

---

## Attributes

| Attribute         | Type   | Description        |
| ----------------- | ------ | ------------------ |
| providerReference | String | External reference |
| providerStatus    | String | Provider lifecycle |
| providerPayload   | JSON   | External metadata  |

---

## Supported Providers

```text id="p1v9wr"
STRIPE
PAYPAL
MERCADOPAGO
ADYEN
```

---

## Behaviors

| Behavior                   | Description    |
| -------------------------- | -------------- |
| synchronizeProviderState() | Reconciliation |

---

# 6. RefundExecution Entity

## Purpose

Represents provider reimbursement execution.

---

## Identity

```text id="g6m2xt"
refundExecutionId
```

---

## Refund Types

```text id="u7m1wr"
FULL_REFUND
PARTIAL_REFUND
```

---

## Attributes

| Attribute               | Type    | Description             |
| ----------------------- | ------- | ----------------------- |
| refundAmount            | Decimal | Reimbursed amount       |
| refundReason            | String  | Reimbursement rationale |
| providerRefundReference | String  | External refund ID      |

---

## Behaviors

| Behavior        | Description            |
| --------------- | ---------------------- |
| executeRefund() | Provider reimbursement |
| rejectRefund()  | Invalid refund         |

---

# 7. WebhookEvent Entity

## Purpose

Represents inbound provider webhook events.

---

## Identity

```text id="m4v8wr"
webhookEventId
```

---

## Attributes

| Attribute  | Type    | Description          |
| ---------- | ------- | -------------------- |
| provider   | String  | Provider source      |
| eventType  | String  | Webhook category     |
| payload    | JSON    | Raw provider payload |
| receivedAt | Instant | Arrival timestamp    |

---

## Critical Challenges

```text id="t5v3xp"
- duplicate delivery
- delayed delivery
- replay attempts
```

---

## Behaviors

| Behavior            | Description         |
| ------------------- | ------------------- |
| validateSignature() | Security validation |
| processWebhook()    | Synchronization     |
| detectReplay()      | Replay protection   |

---

# 8. PaymentRetry Entity

## Purpose

Represents retry orchestration.

---

## Identity

```text id="w2m8vt"
paymentRetryId
```

---

## Attributes

| Attribute   | Type    | Description       |
| ----------- | ------- | ----------------- |
| retryCount  | Integer | Retry attempts    |
| nextRetryAt | Instant | Retry schedule    |
| retryReason | String  | Failure rationale |

---

## Behaviors

| Behavior        | Description         |
| --------------- | ------------------- |
| scheduleRetry() | Retry orchestration |
| abortRetries()  | Permanent failure   |

---

# 9. FraudAssessment Entity

## Purpose

Represents fraud analysis results.

---

## Identity

```text id="q7x1wr"
fraudAssessmentId
```

---

## Examples

```text id="y9v4xp"
- Velocity checks
- Country mismatch
- Excessive retries
```

---

## Attributes

| Attribute      | Type    | Description           |
| -------------- | ------- | --------------------- |
| fraudScore     | Decimal | Risk evaluation       |
| riskLevel      | String  | Threat classification |
| recommendation | String  | Suggested action      |

---

## Behaviors

| Behavior             | Description    |
| -------------------- | -------------- |
| approveTransaction() | Allow payment  |
| blockTransaction()   | Reject payment |

---

# 10. PaymentReconciliation Entity

## Purpose

Represents synchronization validation.

---

## Identity

```text id="f4m7wr"
reconciliationId
```

---

## Examples

```text id="u1x8vt"
Provider says CAPTURED
Local says FAILED
```

---

## Behaviors

| Behavior              | Description            |
| --------------------- | ---------------------- |
| reconcileStates()     | Consistency validation |
| recoverPaymentState() | Recovery orchestration |

---

# 11. ProviderRouting Entity

## Purpose

Represents dynamic provider routing.

---

## Identity

```text id="m6v2wr"
providerRoutingId
```

---

## Examples

```text id="g3x9vp"
LATAM → MercadoPago
US → Stripe
```

---

## Behaviors

| Behavior           | Description     |
| ------------------ | --------------- |
| resolveProvider()  | Dynamic routing |
| failoverProvider() | Provider switch |

---

# 12. PaymentProjection Entity

## Purpose

Represents CQRS-oriented payment views.

---

## Identity

```text id="r5m1xt"
paymentProjectionId
```

---

## Usage

Supports:

* Payment dashboards
* Failure analytics
* Provider metrics

---

# 13. PaymentAuthorization Entity

## Purpose

Represents authorization metadata.

---

## Identity

```text id="x8v4wr"
paymentAuthorizationId
```

---

## Attributes

| Attribute         | Type    | Description             |
| ----------------- | ------- | ----------------------- |
| authorizationCode | String  | Provider authorization  |
| authorizedAt      | Instant | Authorization timestamp |

---

## Behaviors

| Behavior                | Description                |
| ----------------------- | -------------------------- |
| validateAuthorization() | Authorization verification |

---

# 14. PaymentCapture Entity

## Purpose

Represents capture execution metadata.

---

## Identity

```text id="n7m1vt"
paymentCaptureId
```

---

## Attributes

| Attribute      | Type    | Description       |
| -------------- | ------- | ----------------- |
| capturedAmount | Decimal | Captured value    |
| capturedAt     | Instant | Capture timestamp |

---

## Behaviors

| Behavior         | Description           |
| ---------------- | --------------------- |
| executeCapture() | Capture orchestration |

---

# 15. PaymentFailure Entity

## Purpose

Represents transaction failure traceability.

---

## Identity

```text id="k2v7xp"
paymentFailureId
```

---

## Examples

```text id="d1m8wr"
- insufficient funds
- provider timeout
- fraud rejection
```

---

## Behaviors

| Behavior          | Description            |
| ----------------- | ---------------------- |
| classifyFailure() | Failure categorization |

---

# 16. PaymentSession Entity

## Purpose

Represents checkout/payment sessions.

---

## Identity

```text id="h6x2vt"
paymentSessionId
```

---

## Attributes

| Attribute    | Type    | Description        |
| ------------ | ------- | ------------------ |
| sessionToken | String  | Checkout reference |
| expiresAt    | Instant | Session expiration |

---

## Behaviors

| Behavior        | Description          |
| --------------- | -------------------- |
| expireSession() | Session invalidation |

---

# 17. ProviderCredentialReference Entity

## Purpose

Represents secure linkage with external providers.

---

## Identity

```text id="t9v4xp"
providerCredentialReferenceId
```

---

## Important Principle

```text id="j4x9wt"
Provider secrets
must remain externalized
```

---

## Behaviors

| Behavior                     | Description   |
| ---------------------------- | ------------- |
| resolveCredentialReference() | Secure lookup |

---

# 18. WebhookSignature Entity

## Purpose

Represents webhook authenticity verification.

---

## Identity

```text id="m7v1xp"
webhookSignatureId
```

---

## Behaviors

| Behavior            | Description             |
| ------------------- | ----------------------- |
| validateSignature() | Authenticity validation |

---

# 19. PaymentAuditRecord Entity

## Purpose

Represents immutable payment traceability.

---

## Identity

```text id="u5x8wr"
paymentAuditRecordId
```

---

## Examples

```text id="q9m3vt"
AUTHORIZED → CAPTURED
FAILED → RETRY_PENDING
```

---

## Behaviors

| Behavior           | Description            |
| ------------------ | ---------------------- |
| appendAuditEvent() | Immutable traceability |

---

# 20. PaymentNotification Entity

## Purpose

Represents communication related to payments.

---

## Identity

```text id="k1m8vt"
paymentNotificationId
```

---

## Examples

```text id="d2m8wr"
- payment success email
- retry warning
- refund confirmation
```

---

## Behaviors

| Behavior           | Description                 |
| ------------------ | --------------------------- |
| sendNotification() | Communication orchestration |

---

# 21. PaymentDispute Entity

## Purpose

Represents chargebacks and disputes.

---

## Identity

```text id="u8x3wp"
paymentDisputeId
```

---

## Examples

```text id="f6m9wr"
CHARGEBACK
FRAUD_DISPUTE
```

---

## Behaviors

| Behavior         | Description        |
| ---------------- | ------------------ |
| openDispute()    | Dispute creation   |
| resolveDispute() | Dispute resolution |

---

# 22. PaymentSettlement Entity

## Purpose

Represents settlement reconciliation.

---

## Identity

```text id="c8m4xt"
paymentSettlementId
```

---

## Behaviors

| Behavior              | Description           |
| --------------------- | --------------------- |
| reconcileSettlement() | Settlement validation |

---

# 23. Entity Relationships

```text id="u1x8wr"
PaymentTransaction
    ├── linked to -> PaymentMethod
    ├── synchronized with -> ProviderTransaction
    ├── protected by -> FraudAssessment
    ├── coordinated by -> PaymentRetry
    ├── validated by -> PaymentReconciliation
    └── audited by -> PaymentAuditRecord
```

---

# 24. Multi-Tenant Considerations

Tenant-scoped entities:

```text id="w6x3wr"
- PaymentTransaction
- RefundExecution
- PaymentMethod
- PaymentProjection
```

---

# 25. Security-Critical Rules

## Mandatory Protections

| Protection                   | Required |
| ---------------------------- | -------- |
| PCI isolation                | Yes      |
| Tenant isolation             | Yes      |
| Webhook signature validation | Yes      |
| Replay protection            | Yes      |

---

## Forbidden Behavior

```text id="r1m7vp"
Duplicate payment captures
must never occur
```

---

# 26. Lifecycle Considerations

| Entity             | Lifecycle      |
| ------------------ | -------------- |
| PaymentTransaction | Long-term      |
| WebhookEvent       | High-frequency |
| PaymentRetry       | Temporary      |
| FraudAssessment    | Event-driven   |
| PaymentProjection  | Read-optimized |

---

# 27. Future Entity Extensions

Future entities may include:

* CryptoPaymentTransaction
* BNPLTransaction
* SplitPayment
* RealTimeTransfer
* AI FraudAssessment

---

# 28. Summary

The Payment Management entities provide:

* Enterprise-grade payment lifecycle modeling
* PCI-aware boundary isolation
* Reactive payment orchestration
* Distributed provider synchronization
* Fraud-aware transaction governance
* Multi-provider routing support
* Scalable SaaS payment consistency

These entities form the operational foundation of the payment ecosystem.

```
```
