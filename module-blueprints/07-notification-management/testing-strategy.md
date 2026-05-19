# testing-strategy.md

````md
# Notification Management
## Testing Strategy
### CodeCore Module Blueprints
### Version 1.0

---

# 1. SCOPE

* aggregate invariant tests
* template rendering tests
* preference enforcement tests
* provider port contract tests (fakes)
* reactive dispatch pipeline tests
* tenant isolation tests
* idempotency and retry tests

---

# 2. LAYERS

| Layer | Focus |
|-------|-------|
| Domain | invariants, value objects |
| Application | orchestration, event publication |
| Infrastructure | adapter contract tests |
| Integration | event consume/publish with test broker |

---

# 3. CRITICAL SCENARIOS

* duplicate `IdempotencyKey` does not double-send
* preference opt-out blocks marketing but not mandatory compliance category
* failed provider triggers retry then DLQ
* cross-tenant access denied

---

# 4. FORBIDDEN TEST SHORTCUTS

* testing against production providers
* skipping tenant_id validation tests

````
