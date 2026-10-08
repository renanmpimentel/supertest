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

Negative and security tests hide two common gaps:

- **Masking input:** a test asserting "X is ignored" that always sends the legitimate input cannot detect a fallback to X. Add a case with the legitimate input absent and X present.
- **Zero-valued fixture:** when the initial value equals what a regression produces (empty string, 0, nil), "unchanged" and "cleared" look identical. Seed a distinguishable value.

Other gaps that let regressions pass:

- **Indistinguishable fallback:** when the contract is "do not call the dependency" (open circuit, cache hit, retry limit), a fallback returning the same value as the real call proves nothing. Count calls, or make each real response distinct.
- **Setup that never reaches the branch:** an earlier guard (non-configurable property, early return, feature flag) can satisfy the assertion alone. Confirm the setup reaches the branch the test names.
- **Masked strictness cascade:** in strict → lenient fallbacks, a broken strict level is hidden when the next level returns the same answer. Give each level a case where the more lenient level would answer differently.
- **Self-healing state:** later operations can repair a violation (for example, heap polls restoring order). Observe state right after the operation under test.
- **Ignored subject parameter:** a helper receiving the subject (`b Binding`) but asserting through a fixed implementation (`JSON.Bind`) never tests that subject.
- **Global-only pollution check:** asserting `{}.x` is clean misses a replaced prototype on the result itself; also check `Object.getPrototypeOf(result)`.
- **Limit checked only at and far from the bound:** tests at exactly the minimum and far below it cannot tell `fee < MIN` from `fee < MIN - 1`. For every limit, add one case one unit past it on each side that the contract distinguishes (a raw 49 raised to a minimum of 50; a raw 5,001 capped at 5,000).
- **Available oracle:** when a contract says "matches X" (Fetch, a URL parser), compare against X over a corpus covering each input class.

Mental mutation selects regressions; it does not replace execution.

Adapted from Jesse Vincent's [Writing Good Tests](https://github.com/obra/superpowers/blob/main/skills/test-driven-development/writing-good-tests.md) (Superpowers; MIT).
