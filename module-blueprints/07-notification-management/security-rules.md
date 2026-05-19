# security-rules.md

````md
# Notification Management
## Security Rules
### CodeCore Module Blueprints
### Version 1.0

---

# 1. AUTHENTICATION

All APIs require valid JWT issued by **IAM**.

---

# 2. AUTHORIZATION

Sensitive operations require Authorization Management validation:

* send broadcast notifications
* manage templates
* override preferences

---

# 3. TENANT ISOLATION

* mandatory tenant match between token and resource
* cross-tenant send/query forbidden

---

# 4. DATA PROTECTION

* no secrets in notification bodies at rest
* provider credentials only in secret store (Integration/Vault adapters)
* PII minimized in logs; use correlation ids

---

# 5. ABUSE PREVENTION

* rate limiting per tenant and channel
* throttling aligned with Tenant/Subscription quotas (policy port)

````
