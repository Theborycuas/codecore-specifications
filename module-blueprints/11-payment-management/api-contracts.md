# 11-payment-management/api-contracts.md

````md id="s9x4vp"
# Payment Management API Contracts

## 1. Introduction

This document defines the API contracts exposed by the Payment Management module.

The APIs provide capabilities related to:

- Payment authorization
- Payment capture
- Refund execution
- Webhook processing
- Payment retries
- Payment reconciliation
- Fraud validation
- Provider routing
- Settlement synchronization
- Chargeback handling
- Tokenized payment methods
- Payment session orchestration

The contracts are designed following:

- RESTful principles
- Reactive API architecture
- PCI DSS boundary isolation
- Multi-tenant SaaS governance
- Event-driven payment orchestration
- Enterprise financial resilience

---

# 2. API Design Principles

| Principle | Description |
|---|---|
| Tenant-aware APIs | Mandatory |
| PCI boundary isolation | Critical |
| Idempotent operations | Mandatory |
| Reactive-first design | Scalability |
| Provider abstraction | Vendor independence |
| Replay-safe workflows | Reliability |
| Event-driven side effects | Distributed consistency |

---

# 3. Base URL

```text id="u5m1wr"
/api/v1/payments
````

---

# 4. Common Headers

| Header           | Required    | Description         |
| ---------------- | ----------- | ------------------- |
| Authorization    | Yes         | Bearer JWT          |
| X-Tenant-ID      | Yes         | Tenant context      |
| X-Correlation-ID | Recommended | Distributed tracing |
| Content-Type     | Yes         | Request mime type   |
| Idempotency-Key  | Recommended | Retry safety        |

---

# 5. Payment Authorization APIs

# 5.1 Authorize Payment

## Endpoint

```text id="m8v3xp"
POST /transactions/authorize
```

---

## Purpose

Requests payment authorization through a provider.

---

## Request

```json id="f2x7wr"
{
  "invoiceId": "uuid",
  "paymentMethodId": "uuid",
  "amount": 49.99,
  "currency": "USD"
}
```

---

## Response

```json id="r4m9vt"
{
  "success": true,
  "data": {
    "paymentTransactionId": "uuid",
    "status": "AUTHORIZED"
  }
}
```

---

## Side Effects

```text id="x9v1wr"
- Fraud validation
- Provider routing
- Payment events emitted
```

---

## Critical Rule

```text id="k3m8xp"
Authorization
must remain idempotent
```

---

# 5.2 Retrieve Payment Transaction

## Endpoint

```text id="p1v9wr"
GET /transactions/{paymentTransactionId}
```

---

## Response

```json id="g6m2xt"
{
  "success": true,
  "data": {
    "paymentTransactionId": "uuid",
    "provider": "STRIPE",
    "status": "CAPTURED",
    "amount": 49.99
  }
}
```

---

## Security Rules

* Tenant ownership validation mandatory

---

# 6. Payment Capture APIs

# 6.1 Capture Payment

## Endpoint

```text id="u7m1wr"
POST /transactions/{paymentTransactionId}/capture
```

---

## Purpose

Captures authorized funds.

---

## Response

```json id="m4v8wr"
{
  "success": true,
  "data": {
    "status": "CAPTURED"
  }
}
```

---

## Critical Rule

```text id="t5v3xp"
Duplicate captures
must never occur
```

---

# 6.2 Cancel Payment

## Endpoint

```text id="w2m8vt"
POST /transactions/{paymentTransactionId}/cancel
```

---

## Purpose

Cancels pending or authorized transactions.

---

# 7. Refund APIs

# 7.1 Execute Refund

## Endpoint

```text id="q7x1wr"
POST /refunds
```

---

## Request

```json id="y9v4xp"
{
  "paymentTransactionId": "uuid",
  "refundType": "PARTIAL_REFUND",
  "amount": 15.00,
  "reason": "CUSTOMER_REQUEST"
}
```

---

## Response

```json id="f4m7wr"
{
  "success": true,
  "data": {
    "refundExecutionId": "uuid",
    "status": "PROCESSING"
  }
}
```

---

## Refund Types

```text id="u1x8vt"
FULL_REFUND
PARTIAL_REFUND
```

---

# 7.2 Retrieve Refund

## Endpoint

```text id="m6v2wr"
GET /refunds/{refundExecutionId}
```

---

# 8. Webhook APIs

# 8.1 Provider Webhook Endpoint

## Endpoint

```text id="g3x9vp"
POST /webhooks/{provider}
```

---

## Purpose

Receives provider webhook notifications.

---

## Supported Providers

```text id="r5m1xt"
STRIPE
PAYPAL
MERCADOPAGO
ADYEN
```

---

## Critical Requirements

| Requirement          | Mandatory |
| -------------------- | --------- |
| Signature validation | Yes       |
| Replay protection    | Yes       |
| Idempotency          | Yes       |

---

## Important Principle

```text id="x8v4wr"
Webhook processing
must remain replay-safe
```

---

# 9. Retry APIs

# 9.1 Retry Payment

## Endpoint

```text id="n7m1vt"
POST /transactions/{paymentTransactionId}/retry
```

---

## Purpose

Retries transient payment failures.

---

## Examples

```text id="k2v7xp"
- temporary timeout
- provider unavailable
```

---

## Restrictions

| Restriction                  | Description |
| ---------------------------- | ----------- |
| Fraud failures non-retryable | Mandatory   |
| Max retry limit enforced     | Mandatory   |

---

# 10. Reconciliation APIs

# 10.1 Execute Reconciliation

## Endpoint

```text id="d1m8wr"
POST /reconciliation
```

---

## Purpose

Validates provider synchronization.

---

## Response

```json id="h6x2vt"
{
  "success": true,
  "data": {
    "reconciliationId": "uuid",
    "status": "COMPLETED"
  }
}
```

---

## Example

```text id="t9v4xp"
Provider says CAPTURED
Local says FAILED
```

---

# 10.2 Retrieve Reconciliation Report

## Endpoint

```text id="j4x9wt"
GET /reconciliation/reports/{reconciliationId}
```

---

# 11. Fraud APIs

# 11.1 Retrieve Fraud Assessment

## Endpoint

```text id="m7v1xp"
GET /fraud-assessments/{fraudAssessmentId}
```

---

## Response

```json id="u5x8wr"
{
  "success": true,
  "data": {
    "riskLevel": "HIGH",
    "recommendation": "BLOCK"
  }
}
```

---

# 12. Payment Method APIs

# 12.1 Register Tokenized Payment Method

## Endpoint

```text id="q9m3vt"
POST /payment-methods
```

---

## Request

```json id="k1m8vt"
{
  "provider": "STRIPE",
  "paymentToken": "tok_xxx"
}
```

---

## Critical Principle

```text id="d2m8wr"
Raw PCI-sensitive data
must never be received
```

---

# 12.2 Retrieve Payment Methods

## Endpoint

```text id="u8x3wp"
GET /payment-methods
```

---

# 12.3 Deactivate Payment Method

## Endpoint

```text id="f6m9wr"
POST /payment-methods/{paymentMethodId}/deactivate
```

---

# 13. Payment Session APIs

# 13.1 Create Payment Session

## Endpoint

```text id="c8m4xt"
POST /sessions
```

---

## Request

```json id="u1x8wr"
{
  "invoiceId": "uuid",
  "provider": "STRIPE"
}
```

---

## Response

```json id="w6x3wr"
{
  "success": true,
  "data": {
    "sessionId": "uuid",
    "checkoutUrl": "provider-url"
  }
}
```

---

# 13.2 Expire Payment Session

## Endpoint

```text id="r1m7vp"
POST /sessions/{sessionId}/expire
```

---

# 14. Provider Routing APIs

# 14.1 Resolve Provider

## Endpoint

```text id="x4v8xt"
POST /providers/resolve
```

---

## Request

```json id="f2v9xp"
{
  "region": "LATAM",
  "paymentMethodType": "CARD"
}
```

---

## Response

```json id="m6x3vt"
{
  "success": true,
  "data": {
    "provider": "MERCADOPAGO"
  }
}
```

---

# 15. Settlement APIs

# 15.1 Retrieve Settlement Report

## Endpoint

```text id="y5v2wp"
GET /settlements
```

---

## Purpose

Returns settlement synchronization information.

---

# 16. Chargeback APIs

# 16.1 Retrieve Chargebacks

## Endpoint

```text id="m2x7wp"
GET /chargebacks
```

---

## Examples

```text id="q6v3xt"
FRAUD
UNRECOGNIZED_CHARGE
```

---

# 16.2 Retrieve Chargeback

## Endpoint

```text id="h4m9wr"
GET /chargebacks/{chargebackId}
```

---

# 17. Common Response Structure

## Success Response

```json id="d1x8vp"
{
  "success": true,
  "timestamp": "2026-05-20T10:00:00Z",
  "data": {}
}
```

---

## Error Response

```json id="v7m2xt"
{
  "success": false,
  "timestamp": "2026-05-20T10:00:00Z",
  "error": {
    "code": "PAYMENT_ALREADY_CAPTURED",
    "message": "Duplicate capture rejected"
  }
}
```

---

# 18. HTTP Status Codes

| Status | Meaning                  |
| ------ | ------------------------ |
| 200    | Success                  |
| 201    | Created                  |
| 202    | Async processing         |
| 400    | Validation error         |
| 401    | Unauthenticated          |
| 403    | Forbidden                |
| 404    | Resource not found       |
| 409    | Conflict                 |
| 422    | Financial rule violation |
| 429    | Rate limit exceeded      |
| 500    | Internal error           |

---

# 19. Security Rules

## Mandatory Protections

| Protection             | Required |
| ---------------------- | -------- |
| Tenant isolation       | Yes      |
| PCI boundary isolation | Yes      |
| Signature validation   | Yes      |
| Replay protection      | Yes      |
| Idempotency            | Yes      |

---

## Forbidden Behavior

```text id="u5m1wr"
Raw payment credentials
must never cross
domain boundaries
```

---

# 20. Reactive API Considerations

Reactive implementations should support:

```text id="m8v3xp"
Mono<PaymentTransactionResponse>
Flux<WebhookEvent>
Flux<SettlementReport>
```

---

## Requirements

* Non-blocking provider calls
* Reactive reconciliation
* Async retry orchestration
* High-concurrency support

---

# 21. CQRS Considerations

Recommended projections:

| Projection                | Purpose               |
| ------------------------- | --------------------- |
| PaymentProjection         | Fast retrieval        |
| FraudAnalyticsProjection  | Risk analysis         |
| SettlementProjection      | Settlement reporting  |
| ProviderMetricsProjection | Operational analytics |

---

# 22. Distributed System Considerations

The APIs support:

* Multi-region payment orchestration
* Distributed reconciliation
* Event-driven synchronization
* Horizontal scalability
* Replay-safe payment workflows

---

# 23. API Versioning Strategy

Recommended:

```text id="f2x7wr"
/api/v1/payments
```

Future evolution:

```text id="r4m9vt"
/api/v2/payments
```

---

# 24. Error Codes

| Code                      | Description             |
| ------------------------- | ----------------------- |
| PAYMENT_NOT_FOUND         | Missing transaction     |
| PAYMENT_ALREADY_CAPTURED  | Duplicate capture       |
| INVALID_WEBHOOK_SIGNATURE | Security failure        |
| PAYMENT_RETRY_EXHAUSTED   | Retry limit exceeded    |
| PROVIDER_DESYNCHRONIZED   | Reconciliation mismatch |
| FRAUD_BLOCKED             | Fraud prevention        |
| INVALID_REFUND_STATE      | Refund inconsistency    |

---

# 25. Future API Extensions

Future APIs may include:

* Crypto payment APIs
* BNPL APIs
* Real-time transfer APIs
* Marketplace split-payment APIs
* AI fraud APIs

---

# 26. Summary

The Payment Management API contracts provide:

* Enterprise-grade payment APIs
* PCI-aware boundary isolation
* Reactive payment orchestration
* Distributed provider synchronization
* Fraud-aware transaction governance
* Multi-provider routing support
* Scalable SaaS payment resilience

These APIs form the external contract layer of the payment ecosystem.

```
```
