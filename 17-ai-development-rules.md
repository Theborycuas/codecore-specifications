# AI Development Rules
## CodeCore Engineering Specifications
### Version 1.0

---

# 1. PURPOSE

This document defines the official AI Development Rules for CodeCore.

Its objectives are:
- govern AI-assisted development
- preserve architectural consistency
- prevent architectural degradation
- standardize AI-generated code
- improve delivery quality
- maintain modular integrity

---

# 2. AI PHILOSOPHY

AI is considered:
- an implementation accelerator
- not an architectural authority

Architecture is governed by:
- CodeCore specifications
- bounded contexts
- engineering rules

---

# 3. OFFICIAL AI ROLE

AI MAY:
- generate implementations
- generate boilerplate
- generate mappings
- generate tests
- generate documentation

AI MUST NOT:
- redefine architecture
- violate specifications
- invent bounded contexts
- bypass aggregate rules

---

# 4. MANDATORY SPECIFICATION COMPLIANCE

All AI-generated code MUST comply with:
- Aggregate Rules
- Entity Standards
- Repository Rules
- Service Taxonomy
- Reactive Rules
- Multitenancy Rules
- Security Rules

---

# 5. DOMAIN-FIRST RULE

AI MUST prioritize:
- domain integrity
- invariants
- consistency boundaries

before implementation convenience.

---

# 6. REACTIVE ENFORCEMENT RULES

AI-generated code MUST:
- remain non-blocking
- preserve Reactor Context
- avoid .block()
- avoid imperative leakage

---

# 7. MULTITENANCY ENFORCEMENT RULES

AI-generated code MUST:
- preserve tenant isolation
- enforce tenant filtering
- propagate tenant context safely

---

# 8. SECURITY ENFORCEMENT RULES

AI-generated code MUST:
- preserve authorization boundaries
- avoid credential exposure
- preserve JWT integrity
- avoid insecure defaults

---

# 9. AGGREGATE ENFORCEMENT RULES

AI MUST:
- preserve aggregate boundaries
- avoid oversized aggregates
- avoid cross-aggregate mutation

---

# 10. REPOSITORY ENFORCEMENT RULES

AI-generated repositories MUST:
- avoid business orchestration
- remain reactive-friendly
- preserve tenant filtering

---

# 11. SERVICE ENFORCEMENT RULES

AI-generated services MUST:
- remain cohesive
- avoid god services
- preserve transactional clarity

---

# 12. TESTING ENFORCEMENT RULES

AI-generated code MUST include:
- unit tests
- integration tests
- reactive validation
- tenant isolation validation

for critical workflows.

---

# 13. DOCUMENTATION RULES

AI-generated implementations SHOULD include:
- concise documentation
- operational clarity
- architectural traceability

---

# 14. FORBIDDEN AI ANTI-PATTERNS

Forbidden:
- Architecture invention
- Hidden magic behavior
- Blocking reactive code
- Entity exposure
- Tenant-blind implementations
- Generic god services
- Massive repositories
- Shared mutable state
- Ignoring specifications

---

# 15. CURSOR EXECUTION STRATEGY

Cursor MUST operate as:
- senior enterprise engineer
- specification-driven implementer
- architecture-preserving assistant

NOT as:
- autonomous architect

---

# 16. PROMPTING STRATEGY

Prompts SHOULD:
- attach relevant specifications
- define bounded context clearly
- define acceptance criteria
- define architectural restrictions

---

# 17. IMPLEMENTATION GOVERNANCE

Human review remains mandatory for:
- architecture changes
- security-sensitive logic
- multitenancy enforcement
- transactional workflows

---

# 18. CODECORE OFFICIAL AI PHILOSOPHY

AI exists to accelerate implementation
while CodeCore specifications preserve
architectural integrity,
reactive consistency and long-term sustainability.