# 10-billing-management/entities.md

````md id="e9x4vp"
# Billing Management Entities

## 1. Introduction

This document defines the entities of the Billing Management module.

Entities represent financial domain objects that:

- Possess identity
- Maintain financial lifecycle continuity
- Preserve billing traceability
- Govern invoice consistency
- Coordinate financial adjustments
- Support revenue recognition
- Enable tax governance
- Enforce tenant financial isolation

The entities are designed following:

- Domain-Driven Design (DDD)
- Financial immutability principles
- Multi-tenant SaaS governance
- Event-driven billing workflows
- Reactive financial orchestration
- Enterprise compliance requirements

---

# 2. Entity Overview

| Entity | Purpose |
|---|---|
| Invoice | Core financial obligation |
| InvoiceItem | Billable invoice line |
| Charge | Billable operation |
| BillingCycle | Recurring financial period |
| UsageCharge | Consumption monetization |
| TaxCalculation | Tax computation |
| CreditNote | Financial correction |
| Refund | Reimbursement lifecycle |
| BillingAdjustment | Invoice modification |
| RevenueRecord | Revenue traceability |
| OverageCharge | Excess usage billing |
| SeatCharge | Seat monetization |
| AddonCharge | Add-on monetization |
| FinancialReconciliation | Consistency verification |
| BillingProjection | CQRS financial projection |
| PaymentReference | Payment linkage |
| InvoiceStatusHistory | Lifecycle traceability |
| DiscountRecord | Promotional reduction |
| ProrationRecord | Partial billing adjustment |
| CurrencyExchangeRecord | Multi-currency conversion |
| BillingAccount | Tenant financial profile |
| TaxJurisdiction | Regional taxation |
| InvoiceAttachment | Supporting financial document |

---

# 3. Invoice Entity

## Purpose

Represents the central financial document issued to a tenant.

---

## Identity

```text id="u5m1wr"
invoiceId
````

---

## Attributes

| Attribute     | Type    | Description              |
| ------------- | ------- | ------------------------ |
| invoiceId     | UUID    | Invoice identifier       |
| tenantId      | UUID    | Owning tenant            |
| invoiceNumber | String  | Human-readable reference |
| status        | String  | Invoice lifecycle        |
| issuedAt      | Instant | Issue timestamp          |
| dueDate       | Instant | Payment deadline         |
| totalAmount   | Decimal | Invoice total            |
| currency      | String  | Billing currency         |
| createdAt     | Instant | Creation timestamp       |

---

## Lifecycle States

```text id="m8v3xp"
DRAFT
PENDING
ISSUED
PAID
OVERDUE
VOIDED
REFUNDED
```

---

## Behaviors

| Behavior        | Description              |
| --------------- | ------------------------ |
| issueInvoice()  | Issues invoice           |
| markAsPaid()    | Registers payment        |
| markAsOverdue() | Applies overdue state    |
| voidInvoice()   | Cancels invoice validity |

---

## Critical Rules

| Rule                       | Description         |
| -------------------------- | ------------------- |
| Issued invoices immutable  | Financial integrity |
| Negative totals forbidden  | Validation          |
| Tenant ownership mandatory | Isolation           |

---

# 4. InvoiceItem Entity

## Purpose

Represents a billable line inside an invoice.

---

## Identity

```text id="f2x7wr"
invoiceItemId
```

---

## Examples

```text id="r4m9vt"
- Monthly subscription
- AI token usage
- Extra storage
```

---

## Attributes

| Attribute   | Type    | Description           |
| ----------- | ------- | --------------------- |
| description | String  | Human-readable detail |
| quantity    | Decimal | Billable quantity     |
| unitPrice   | Decimal | Price per unit        |
| subtotal    | Decimal | Line total            |

---

## Behaviors

| Behavior            | Description           |
| ------------------- | --------------------- |
| calculateSubtotal() | Financial calculation |

---

# 5. Charge Entity

## Purpose

Represents a billable financial operation.

---

## Identity

```text id="x9v1wr"
chargeId
```

---

## Attributes

| Attribute  | Type    | Description      |
| ---------- | ------- | ---------------- |
| chargeType | String  | Billing category |
| amount     | Decimal | Charge amount    |
| chargeDate | Instant | Charge timestamp |

---

## Examples

```text id="k3m8xp"
SUBSCRIPTION
USAGE
ADDON
OVERAGE
```

---

## Behaviors

| Behavior              | Description        |
| --------------------- | ------------------ |
| generateInvoiceItem() | Billing conversion |

---

# 6. BillingCycle Entity

## Purpose

Represents recurring billing cadence.

---

## Identity

```text id="p1v9wr"
billingCycleId
```

---

## Examples

```text id="g6m2xt"
MONTHLY
QUARTERLY
YEARLY
```

---

## Behaviors

| Behavior        | Description             |
| --------------- | ----------------------- |
| nextCycleDate() | Calculates next billing |

---

# 7. UsageCharge Entity

## Purpose

Represents consumption-based billing.

---

## Identity

```text id="u7m1wr"
usageChargeId
```

---

## Examples

```text id="m4v8wr"
- AI token billing
- Storage billing
- API request billing
```

---

## Attributes

| Attribute        | Type    | Description      |
| ---------------- | ------- | ---------------- |
| resourceType     | String  | Metered resource |
| consumedUnits    | Decimal | Usage quantity   |
| calculatedAmount | Decimal | Monetized total  |

---

## Behaviors

| Behavior             | Description        |
| -------------------- | ------------------ |
| calculateUsageCost() | Usage monetization |

---

# 8. TaxCalculation Entity

## Purpose

Represents tax computation results.

---

## Identity

```text id="t5v3xp"
taxCalculationId
```

---

## Examples

```text id="w2m8vt"
VAT
IVA
GST
```

---

## Attributes

| Attribute     | Type    | Description        |
| ------------- | ------- | ------------------ |
| taxRate       | Decimal | Applied percentage |
| taxableAmount | Decimal | Taxable base       |
| calculatedTax | Decimal | Tax amount         |

---

## Behaviors

| Behavior       | Description     |
| -------------- | --------------- |
| calculateTax() | Tax computation |

---

## Critical Rules

* Tax calculations must remain auditable
* Historical tax rules preserved

---

# 9. CreditNote Entity

## Purpose

Represents invoice compensation documents.

---

## Identity

```text id="q7x1wr"
creditNoteId
```

---

## Attributes

| Attribute         | Type    | Description         |
| ----------------- | ------- | ------------------- |
| originalInvoiceId | UUID    | Corrected invoice   |
| creditAmount      | Decimal | Compensation amount |
| reason            | String  | Financial rationale |

---

## Behaviors

| Behavior      | Description          |
| ------------- | -------------------- |
| applyCredit() | Financial adjustment |

---

# 10. Refund Entity

## Purpose

Represents reimbursements issued to tenants.

---

## Identity

```text id="y9v4xp"
refundId
```

---

## Refund Types

```text id="f4m7wr"
FULL_REFUND
PARTIAL_REFUND
```

---

## Attributes

| Attribute      | Type    | Description             |
| -------------- | ------- | ----------------------- |
| refundedAmount | Decimal | Returned amount         |
| refundReason   | String  | Reimbursement rationale |
| refundedAt     | Instant | Refund timestamp        |

---

## Behaviors

| Behavior        | Description          |
| --------------- | -------------------- |
| approveRefund() | Refund authorization |
| rejectRefund()  | Refund rejection     |

---

# 11. BillingAdjustment Entity

## Purpose

Represents financial modifications.

---

## Identity

```text id="u1x8vt"
billingAdjustmentId
```

---

## Examples

```text id="m6v2wr"
- Upgrade proration
- Discount correction
- Invoice compensation
```

---

## Behaviors

| Behavior          | Description          |
| ----------------- | -------------------- |
| applyAdjustment() | Billing modification |

---

# 12. RevenueRecord Entity

## Purpose

Represents recognized revenue entries.

---

## Identity

```text id="g3x9vp"
revenueRecordId
```

---

## Attributes

| Attribute        | Type    | Description       |
| ---------------- | ------- | ----------------- |
| recognizedAmount | Decimal | Revenue amount    |
| recognitionDate  | Instant | Revenue timestamp |

---

## Important Principle

```text id="r5m1xt"
Revenue records
must remain immutable
```

---

# 13. OverageCharge Entity

## Purpose

Represents monetization of excess usage.

---

## Identity

```text id="x8v4wr"
overageChargeId
```

---

## Examples

```text id="n7m1vt"
AI quota exceeded
```

---

## Behaviors

| Behavior           | Description         |
| ------------------ | ------------------- |
| calculateOverage() | Excess monetization |

---

# 14. SeatCharge Entity

## Purpose

Represents seat/user licensing charges.

---

## Identity

```text id="k2v7xp"
seatChargeId
```

---

## Attributes

| Attribute     | Type    | Description       |
| ------------- | ------- | ----------------- |
| seatCount     | Integer | Licensed seats    |
| includedSeats | Integer | Included quantity |
| extraSeats    | Integer | Overage seats     |

---

## Behaviors

| Behavior               | Description       |
| ---------------------- | ----------------- |
| calculateSeatCharges() | Seat monetization |

---

# 15. AddonCharge Entity

## Purpose

Represents optional commercial extension billing.

---

## Identity

```text id="d1m8wr"
addonChargeId
```

---

## Examples

```text id="h6x2vt"
- Extra storage
- AI package
```

---

## Behaviors

| Behavior               | Description            |
| ---------------------- | ---------------------- |
| activateAddonBilling() | Generates billing item |

---

# 16. FinancialReconciliation Entity

## Purpose

Represents consistency verification.

---

## Identity

```text id="t9v4xp"
reconciliationId
```

---

## Behaviors

| Behavior                       | Description            |
| ------------------------------ | ---------------------- |
| validateFinancialConsistency() | Integrity verification |

---

## Example

```text id="j4x9wt"
Invoice total
=
Charges
+
Taxes
-
Credits
```

---

# 17. BillingProjection Entity

## Purpose

Represents read-optimized billing views.

---

## Identity

```text id="m7v1xp"
billingProjectionId
```

---

## Usage

Supports:

* Dashboards
* Revenue analytics
* Invoice reporting

---

# 18. PaymentReference Entity

## Purpose

Represents linkage with Payment Management.

---

## Identity

```text id="u5x8wr"
paymentReferenceId
```

---

## Important Principle

```text id="q9m3vt"
Payment execution
remains external
```

---

# 19. InvoiceStatusHistory Entity

## Purpose

Represents immutable lifecycle traceability.

---

## Identity

```text id="k1m8vt"
invoiceStatusHistoryId
```

---

## Examples

```text id="d2m8wr"
DRAFT → ISSUED
ISSUED → PAID
```

---

## Behaviors

| Behavior             | Description           |
| -------------------- | --------------------- |
| appendStatusChange() | Lifecycle audit trail |

---

# 20. DiscountRecord Entity

## Purpose

Represents promotional reductions.

---

## Identity

```text id="u8x3wp"
discountRecordId
```

---

## Examples

```text id="f6m9wr"
PROMOTIONAL_DISCOUNT
LOYALTY_DISCOUNT
```

---

## Behaviors

| Behavior            | Description         |
| ------------------- | ------------------- |
| calculateDiscount() | Discount evaluation |

---

# 21. ProrationRecord Entity

## Purpose

Represents partial billing calculations.

---

## Identity

```text id="c8m4xt"
prorationRecordId
```

---

## Usage Examples

```text id="u1x8wr"
PRO → BUSINESS
mid-cycle upgrade
```

---

## Behaviors

| Behavior             | Description                |
| -------------------- | -------------------------- |
| calculateProration() | Partial charge computation |

---

# 22. CurrencyExchangeRecord Entity

## Purpose

Represents multi-currency conversion traceability.

---

## Identity

```text id="w6x3wr"
currencyExchangeRecordId
```

---

## Attributes

| Attribute      | Type    | Description        |
| -------------- | ------- | ------------------ |
| sourceCurrency | String  | Original currency  |
| targetCurrency | String  | Converted currency |
| exchangeRate   | Decimal | Applied rate       |

---

# 23. BillingAccount Entity

## Purpose

Represents tenant financial identity.

---

## Identity

```text id="r1m7vp"
billingAccountId
```

---

## Attributes

| Attribute      | Type   | Description       |
| -------------- | ------ | ----------------- |
| tenantId       | UUID   | Tenant owner      |
| billingEmail   | String | Financial contact |
| billingAddress | String | Legal address     |

---

# 24. TaxJurisdiction Entity

## Purpose

Represents regional taxation context.

---

## Identity

```text id="x4v8xt"
taxJurisdictionId
```

---

## Examples

```text id="f2v9xp"
ECUADOR
EU
US_CA
```

---

## Behaviors

| Behavior                 | Description       |
| ------------------------ | ----------------- |
| resolveApplicableTaxes() | Tax determination |

---

# 25. InvoiceAttachment Entity

## Purpose

Represents supporting financial documents.

---

## Identity

```text id="m6x3vt"
invoiceAttachmentId
```

---

## Examples

```text id="y5v2wp"
PDF invoice
Tax receipt
```

---

## Behaviors

| Behavior         | Description      |
| ---------------- | ---------------- |
| attachDocument() | Document linkage |

---

# 26. Entity Relationships

```text id="m2x7wp"
Invoice
    ├── owns -> InvoiceItem
    ├── linked to -> Charge
    ├── governed by -> TaxCalculation
    ├── linked to -> CreditNote
    ├── linked to -> Refund
    ├── adjusted by -> BillingAdjustment
    └── tracked by -> RevenueRecord
```

---

# 27. Multi-Tenant Considerations

Tenant-scoped entities:

```text id="q6v3xt"
- Invoice
- Charge
- RevenueRecord
- Refund
- BillingAccount
```

---

# 28. Security-Critical Rules

## Mandatory Protections

| Protection             | Required |
| ---------------------- | -------- |
| Tenant isolation       | Yes      |
| Invoice immutability   | Yes      |
| Financial traceability | Yes      |
| Tax auditability       | Yes      |

---

## Forbidden Behavior

```text id="h4m9wr"
Issued invoices
must not be silently modified
```

---

# 29. Lifecycle Considerations

| Entity            | Lifecycle      |
| ----------------- | -------------- |
| Invoice           | Long-term      |
| UsageCharge       | High-frequency |
| Refund            | Event-driven   |
| RevenueRecord     | Immutable      |
| BillingProjection | Read-optimized |

---

# 30. Future Entity Extensions

Future entities may include:

* MarketplaceInvoice
* EnterpriseContractCharge
* AIConsumptionCharge
* RegionalTaxRule
* DynamicPricingAdjustment
* RevenueForecast

---

# 31. Summary

The Billing Management entities provide:

* Enterprise-grade invoice lifecycle modeling
* Multi-tenant financial isolation
* Reactive billing orchestration
* Distributed financial consistency
* Immutable revenue traceability
* Usage-based monetization support
* Scalable SaaS financial governance

These entities form the operational foundation of the billing ecosystem.

```
```
