# DTO Standards
## CodeCore Engineering Specifications
### Version 1.0

---

# 1. PURPOSE

This document defines the official DTO Standards for CodeCore.

Its objectives are:
- preserve architectural boundaries
- avoid entity exposure
- standardize API contracts
- improve serialization safety
- optimize reactive payloads
- prevent domain leakage
- guide AI-assisted development

---

# 2. DTO PHILOSOPHY

DTOs exist to:
- expose controlled contracts
- isolate domain internals
- optimize transport structures
- stabilize external communication

DTOs are NOT:
- entities
- aggregates
- persistence models

---

# 3. DTO CLASSIFICATION

Official DTO categories:

| Type | Purpose |
|---|---|
| Request DTO | Input contracts |
| Response DTO | Output contracts |
| Query DTO | Optimized reads |
| Event DTO | Event payloads |
| Internal DTO | Inter-service communication |

---

# 4. DOMAIN ISOLATION RULES

Entities MUST NEVER be exposed directly through APIs.

Forbidden:

- returning entities from controllers
- serializing aggregates directly
- exposing internal entity graphs

---

# 5. REQUEST DTO RULES

Request DTOs:
- represent external intent
- validate structure
- remain immutable
- avoid business logic

---

# 6. RESPONSE DTO RULES

Response DTOs:
- expose minimal required information
- avoid internal persistence details
- remain stable and versionable

---

# 7. IMMUTABILITY RULES

DTOs SHOULD remain immutable.

Preferred:
- Java Records
- final fields

---

# 8. MAPPING RULES

Mappings MUST remain explicit.

Forbidden:
- magic reflection mapping
- hidden implicit mapping

Preferred:
- MapStruct
- explicit mappers

---

# 9. VALIDATION RULES

Request DTO validation SHOULD occur:
- before application services
- before domain execution

Preferred:
- Bean Validation

---

# 10. REACTIVE RULES

DTO handling MUST remain:
- non-blocking
- lightweight
- serialization-safe

---

# 11. MULTITENANCY RULES

Tenant-sensitive DTOs MUST:
- preserve tenant isolation
- avoid cross-tenant exposure

---

# 12. SECURITY RULES

DTOs MUST NEVER expose:
- passwords
- tokens
- secrets
- internal permissions unintentionally

---

# 13. VERSIONING RULES

Public DTOs SHOULD support:
- backward compatibility
- explicit evolution

---

# 14. FORBIDDEN DTO ANTI-PATTERNS

Forbidden:
- Entity exposure
- Bidirectional DTO graphs
- Oversized payloads
- Mutable DTOs
- Persistence annotations inside DTOs

---

# 15. AI IMPLEMENTATION RULES

All AI-generated DTOs MUST:
- remain immutable
- avoid domain leakage
- avoid entity exposure
- preserve serialization safety
- preserve tenant isolation

---

# 16. CODECORE OFFICIAL DTO PHILOSOPHY

DTOs exist to expose stable,
minimal and secure contracts
without leaking domain internals.