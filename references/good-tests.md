# Good tests

When auditing and correcting existing tests:

- Name the concrete defect the test must detect.
- Derive expectations independently of implementation and its helpers; use literals or checked fixtures.
- Observe requirements; avoid alarms for internal changes that preserve contracts.
- At library boundaries, test the application's contract rather than framework mechanics alone.
- Preserve necessary effects and faithful contract data in mocks; replace the external operation below those effects.
- Move test-only helpers to test utilities. Keep application methods managing owned resources, even if only tests call them.

Contract: remove surrounding spaces.

```python
def normalize(text):
    return text.strip()

# Circular: still passes if normalize returns spaces.
assert normalize(" name ") == normalize(" name ")
# Independent: protects the contract.
assert normalize(" name ") == "name"
```

Text, exact values, and interactions can be legitimate contracts. Spies may check contractual arguments, counts, or order.

Mental mutation selects regressions; it does not replace execution.

Adapted from Jesse Vincent's [Writing Good Tests](https://github.com/obra/superpowers/blob/main/skills/test-driven-development/writing-good-tests.md) (Superpowers; MIT).
