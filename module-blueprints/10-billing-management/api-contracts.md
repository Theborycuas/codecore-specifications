# 10-billing-management/api-contracts.md

````md id="i9x4vp"
# Billing Management API Contracts

## 1. Introduction

This document defines the API contracts exposed by the Billing Management module.

The APIs provide capabilities related to:

- Invoice lifecycle management
- Financial charge generation
- Usage billing
- Tax calculation
- Refund management
- Credit note handling
- Revenue reporting
- Billing adjustments
- Financial reconciliation
- Tenant financial governance

The contracts are designed following:

- RESTful principles
- Reactive API architecture
- Multi-tenant SaaS isolation
- Financial immutability principles
- Enterprise billing compliance
- Event-driven monetization workflows

---

# 2. API Design Principles

| Principle | Description |
|---|---|
| Tenant-aware APIs | Mandatory |
| Financial traceability | Required |
| Idempotent operations | Critical |
| Reactive-first design | Scalability |
| Immutable financial history | Compliance |
| Event-driven side effects | Distributed consistency |

---

# 3. Base URL

```text id="u5m1wr"
/api/v1/billing
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

# 5. Invoice APIs

# 5.1 Generate Invoice

## Endpoint

```text id="m8v3xp"
POST /invoices
```

---

## Purpose

Generates a financial obligation.

---

## Request

```json id="f2x7wr"
{
  "tenantId": "uuid",
  "billingPeriod": {
    "from": "2026-01-01",
    "to": "2026-01-31"
  }
}
```

---

## Response

```json id="r4m9vt"
{
  "success": true,
  "data": {
    "invoiceId": "uuid",
    "status": "DRAFT"
  }
}
```

---

## Side Effects

```text id="x9v1wr"
- Charges aggregated
- Taxes calculated
- Events emitted
```

---

# 5.2 Retrieve Invoice

## Endpoint

```text id="k3m8xp"
GET /invoices/{invoiceId}
```

---

## Response

```json id="p1v9wr"
{
  "success": true,
  "data": {
    "invoiceId": "uuid",
    "invoiceNumber": "INV-2026-00001",
    "status": "ISSUED",
    "totalAmount": 120.50,
    "currency": "USD"
  }
}
```

---

## Security Rules

* Tenant ownership validation mandatory

---

# 5.3 List Tenant Invoices

## Endpoint

```text id="g6m2xt"
GET /tenant/{tenantId}/invoices
```

---

## Query Parameters

| Parameter | Description       |
| --------- | ----------------- |
| status    | Invoice lifecycle |
| from      | Start date        |
| to        | End date          |
| page      | Pagination        |
| size      | Page size         |

---

# 5.4 Issue Invoice

## Endpoint

```text id="u7m1wr"
POST /invoices/{invoiceId}/issue
```

---

## Purpose

Transitions invoice into financially valid state.

---

## Critical Rule

```text id="m4v8wr"
Issued invoices
must become immutable
```

---

# 5.5 Void Invoice

## Endpoint

```text id="t5v3xp"
POST /invoices/{invoiceId}/void
```

---

## Request

```json id="w2m8vt"
{
  "reason": "BILLING_ERROR"
}
```

---

## Important Principle

```text id="q7x1wr"
Invoices should not be deleted
```

---

# 5.6 Mark Invoice Overdue

## Endpoint

```text id="y9v4xp"
POST /invoices/{invoiceId}/overdue
```

---

## Purpose

Applies overdue financial status.

---

# 6. Invoice Item APIs

# 6.1 Add Invoice Item

## Endpoint

```text id="f4m7wr"
POST /invoices/{invoiceId}/items
```

---

## Request

```json id="u1x8vt"
{
  "description": "AI token usage",
  "quantity": 10000,
  "unitPrice": 0.002
}
```

---

## Response

```json id="m6v2wr"
{
  "success": true,
  "data": {
    "invoiceItemId": "uuid",
    "subtotal": 20.00
  }
}
```

---

## Restrictions

| Restriction               | Description |
| ------------------------- | ----------- |
| Issued invoices immutable | Mandatory   |
| Currency consistency      | Required    |

---

# 6.2 Remove Invoice Item

## Endpoint

```text id="g3x9vp"
DELETE /invoices/{invoiceId}/items/{invoiceItemId}
```

---

## Important Rule

```text id="r5m1xt"
Invoice items
cannot be removed
after invoice issuance
```

---

# 7. Charge APIs

# 7.1 Generate Charge

## Endpoint

```text id="x8v4wr"
POST /charges
```

---

## Request

```json id="n7m1vt"
{
  "tenantId": "uuid",
  "chargeType": "USAGE",
  "amount": 25.00
}
```

---

## Examples

```text id="k2v7xp"
SUBSCRIPTION
USAGE
ADDON
OVERAGE
```

---

# 7.2 Retrieve Charges

## Endpoint

```text id="d1m8wr"
GET /tenant/{tenantId}/charges
```

---

## Purpose

Returns tenant billable operations.

---

# 8. Usage Billing APIs

# 8.1 Generate Usage Charges

## Endpoint

```text id="h6x2vt"
POST /usage/charges
```

---

## Request

```json id="t9v4xp"
{
  "tenantId": "uuid",
  "resourceType": "AI_TOKENS",
  "consumedUnits": 10000
}
```

---

## Side Effects

```text id="j4x9wt"
- Usage aggregation
- Billing calculation
- Invoice item generation
```

---

# 8.2 Retrieve Usage Billing

## Endpoint

```text id="m7v1xp"
GET /tenant/{tenantId}/usage-billing
```

---

# 9. Overage Billing APIs

# 9.1 Generate Overage Charges

## Endpoint

```text id="u5x8wr"
POST /overages
```

---

## Request

```json id="q9m3vt"
{
  "tenantId": "uuid",
  "resourceType": "API_REQUESTS",
  "excessUnits": 5000
}
```

---

## Overage Policies

```text id="k1m8vt"
HARD_LIMIT
SOFT_LIMIT
PAY_PER_USE
```

---

# 10. Seat Billing APIs

# 10.1 Generate Seat Charges

## Endpoint

```text id="d2m8wr"
POST /seat-billing
```

---

## Request

```json id="u8x3wp"
{
  "tenantId": "uuid",
  "activeSeats": 60
}
```

---

## Example

```text id="f6m9wr"
BUSINESS plan
50 included seats
→ extra seats billed
```

---

# 11. Addon Billing APIs

# 11.1 Generate Addon Charges

## Endpoint

```text id="c8m4xt"
POST /addon-billing
```

---

## Request

```json id="u1x8wr"
{
  "tenantId": "uuid",
  "addonCode": "AI_PACKAGE"
}
```

---

# 12. Tax APIs

# 12.1 Calculate Taxes

## Endpoint

```text id="w6x3wr"
POST /tax/calculate
```

---

## Request

```json id="r1m7vp"
{
  "tenantId": "uuid",
  "taxableAmount": 100.00,
  "jurisdiction": "ECUADOR"
}
```

---

## Response

```json id="x4v8xt"
{
  "success": true,
  "data": {
    "taxAmount": 12.00
  }
}
```

---

## Examples

```text id="f2v9xp"
VAT
IVA
GST
```

---

# 12.2 Retrieve Tax Records

## Endpoint

```text id="m6x3vt"
GET /tenant/{tenantId}/taxes
```

---

# 13. Discount APIs

# 13.1 Apply Discount

## Endpoint

```text id="y5v2wp"
POST /discounts
```

---

## Request

```json id="m2x7wp"
{
  "invoiceId": "uuid",
  "discountType": "PROMOTIONAL",
  "discountAmount": 20.00
}
```

---

## Side Effects

```text id="q6v3xt"
- Invoice recalculation
- Audit registration
```

---

# 14. Proration APIs

# 14.1 Calculate Proration

## Endpoint

```text id="h4m9wr"
POST /proration/calculate
```

---

## Request

```json id="d1x8vp"
{
  "tenantId": "uuid",
  "oldPlan": "PRO",
  "newPlan": "BUSINESS"
}
```

---

## Important Principle

```text id="v7m2xt"
Proration calculations
must remain deterministic
```

---

# 15. Refund APIs

# 15.1 Request Refund

## Endpoint

```text id="u5m1wr"
POST /refunds
```

---

## Request

```json id="m8v3xp"
{
  "invoiceId": "uuid",
  "refundType": "PARTIAL_REFUND",
  "amount": 15.00
}
```

---

## Refund Types

```text id="f2x7wr"
FULL_REFUND
PARTIAL_REFUND
```

---

# 15.2 Approve Refund

## Endpoint

```text id="r4m9vt"
POST /refunds/{refundId}/approve
```

---

# 15.3 Reject Refund

## Endpoint

```text id="x9v1wr"
POST /refunds/{refundId}/reject
```

---

# 16. Credit Note APIs

# 16.1 Create Credit Note

## Endpoint

```text id="k3m8xp"
POST /credit-notes
```

---

## Request

```json id="p1v9wr"
{
  "invoiceId": "uuid",
  "creditAmount": 10.00,
  "reason": "OVERCHARGE"
}
```

---

## Side Effects

```text id="g6m2xt"
- Financial compensation
- Revenue correction
```

---

# 17. Billing Account APIs

# 17.1 Create Billing Account

## Endpoint

```text id="u7m1wr"
POST /billing-accounts
```

---

## Request

```json id="m4v8wr"
{
  "tenantId": "uuid",
  "billingEmail": "finance@tenant.com",
  "billingAddress": {
    "country": "EC",
    "city": "Quito"
  }
}
```

---

## Response

```json id="t5v3xp"
{
  "success": true,
  "data": {
    "billingAccountId": "uuid"
  }
}
```

---

# 17.2 Retrieve Billing Account

## Endpoint

```text id="w2m8vt"
GET /billing-accounts/{billingAccountId}
```

---

# 18. Revenue APIs

# 18.1 Retrieve Revenue Metrics

## Endpoint

```text id="q7x1wr"
GET /analytics/revenue
```

---

## Examples

```text id="y9v4xp"
- Monthly recurring revenue
- Usage revenue
- Refund ratios
```

---

# 18.2 Retrieve Billing Analytics

## Endpoint

```text id="f4m7wr"
GET /analytics/billing
```

---

# 19. Financial Reconciliation APIs

# 19.1 Execute Reconciliation

## Endpoint

```text id="u1x8vt"
POST /reconciliation
```

---

## Purpose

Validates billing consistency.

---

## Example

```text id="m6v2wr"
Invoice total
=
Charges
+
Taxes
-
Credits
```

---

# 19.2 Retrieve Reconciliation Reports

## Endpoint

```text id="g3x9vp"
GET /reconciliation/reports
```

---

# 20. Common Response Structure

## Success Response

```json id="r5m1xt"
{
  "success": true,
  "timestamp": "2026-05-20T10:00:00Z",
  "data": {}
}
```

---

## Error Response

```json id="x8v4wr"
{
  "success": false,
  "timestamp": "2026-05-20T10:00:00Z",
  "error": {
    "code": "INVOICE_ALREADY_ISSUED",
    "message": "Invoice is immutable"
  }
}
```

---

# 21. HTTP Status Codes

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

# 22. Security Rules

## Mandatory Protections

| Protection                 | Required |
| -------------------------- | -------- |
| Tenant financial isolation | Yes      |
| Invoice immutability       | Yes      |
| Tax traceability           | Yes      |
| Refund authorization       | Yes      |

---

## Forbidden Behavior

```text id="n7m1vt"
Issued invoices
must not be silently modified
```

---

# 23. Reactive API Considerations

Reactive implementations should support:

```text id="k2v7xp"
Mono<InvoiceResponse>
Flux<ChargeResponse>
Flux<RevenueMetric>
```

---

## Requirements

* Non-blocking invoice generation
* Reactive reconciliation
* High-concurrency support
* Async financial projections

---

# 24. CQRS Considerations

Recommended projections:

| Projection                 | Purpose              |
| -------------------------- | -------------------- |
| InvoiceProjection          | Fast retrieval       |
| RevenueProjection          | Financial analytics  |
| BillingDashboardProjection | Reporting            |
| TaxProjection              | Compliance reporting |

---

# 25. Distributed System Considerations

The APIs support:

* Multi-region SaaS billing
* Distributed reconciliation
* Event-driven consistency
* Horizontal scalability
* Replay-safe workflows

---

# 26. API Versioning Strategy

Recommended:

```text id="d1m8wr"
/api/v1/billing
```

Future evolution:

```text id="h6x2vt"
/api/v2/billing
```

---

# 27. Error Codes

| Code                      | Description            |
| ------------------------- | ---------------------- |
| INVOICE_NOT_FOUND         | Missing invoice        |
| INVOICE_ALREADY_ISSUED    | Immutable invoice      |
| INVALID_TAX_CONFIGURATION | Tax issue              |
| INVALID_REFUND_AMOUNT     | Refund validation      |
| FINANCIAL_INCONSISTENCY   | Reconciliation failure |
| OVERAGE_POLICY_VIOLATION  | Overage restriction    |
| INVALID_PRORATION         | Billing inconsistency  |

---

# 28. Summary

The Billing Management API contracts provide:

* Enterprise-grade financial APIs
* Multi-tenant billing isolation
* Reactive invoice orchestration
* Distributed financial consistency
* Usage-based monetization support
* Immutable financial traceability
* Scalable SaaS billing governance

These APIs form the external contract layer of the billing ecosystem.

```
```
