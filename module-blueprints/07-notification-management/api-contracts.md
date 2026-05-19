# api-contracts.md

````md
# Notification Management
## API Contracts
### CodeCore Module Blueprints
### Version 1.0

---

# 1. PRINCIPLES

* tenant-scoped APIs
* authorization required (Authorization Management)
* reactive endpoints (WebFlux)
* no provider secrets in responses

---

# 2. ADMIN / OPERATIONAL APIs

| Method | Path | Purpose |
|--------|------|---------|
| POST | `/notifications` | Request ad-hoc notification (authorized) |
| GET | `/notifications/{id}` | Delivery status |
| GET | `/notifications` | Paginated search (tenant-scoped) |
| POST | `/templates` | Create template |
| PUT | `/templates/{id}/versions/{version}/activate` | Activate template version |
| GET | `/templates` | List templates |
| PUT | `/preferences/{recipientId}` | Update preferences |
| GET | `/preferences/{recipientId}` | Read preferences |

---

# 3. IN-APP APIs

| Method | Path | Purpose |
|--------|------|---------|
| GET | `/in-app/notifications` | User inbox (current tenant) |
| PATCH | `/in-app/notifications/{id}/read` | Mark read |

---

# 4. FORBIDDEN

* public unauthenticated bulk send
* cross-tenant notification queries

````
