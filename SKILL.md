---
name: supertest
description: Use when auditing or improving existing unit and integration tests, investigating tests that pass without observing outcomes, or analyzing application mutations and Necessist findings.
---

# Supertest

## Overview and when to use

Audit existing tests against contracts. Deliver corrections, demonstrated regressions, and classified findings with evidence and limitations.

## Core rule

**Approve a test's effectiveness only after observing it detect the expected defect.** A green suite establishes the baseline, not effectiveness.

Introduce temporary regressions in an isolated copy containing relevant local changes; restore correct behavior afterward. Preserve correct legacy code: this TDD adaptation requires neither deleting nor rewriting it. Compilation or environment failures do not prove defect detection.

## Audit cycle

### 1. Discover and run

Read instructions, contracts, diff, stack, and commands. Include unchanged tests protecting the scope. Verify write access, required services, and all test connection settings before execution.

Run the relevant suite; record command, scope, and collected, passed, failed, and skipped counts. Require a verified, stable baseline with actual collection before interpreting tools.

### 2. Audit unit tests

Check rules, boundaries, invalid inputs, errors, and observable outcomes. Derive expectations from contracts without copying the algorithm. Avoid tautologies and mocking core behavior; replace necessary boundaries. Consult [good tests](references/good-tests.md) when evaluating expectations, mocks, and helpers.

Await promises and prove callbacks execute: assertions inside an uncalled callback protect nothing.

### 3. Audit integration tests

Exercise real communication, persistence, transactions, and required effects. HTTP 201 alone does not prove a write.

Use isolated data and services, deterministic setup, and cleanup even after failures. Check order independence.

### 4. Evaluate effectiveness

Run application mutation testing and Necessist when compatible. Consult [tools](references/tools.md) for selection and configuration.

In the isolated copy, run tools sequentially on the same files, restoring state between analyses. Record versions, commands, scope, collected tests, candidates, and reports. Reproduce survivors and `passed` candidates; classify findings and limitations. Never present manual review as execution.

### 5. Correct and verify

Apply the smallest permanent test correction to the original project by default. In the isolated copy, show it failing on the expected regression and passing after restoration. For Necessist defects, repeat the relevant removal.

Rerun affected analyses and the original project's required lint, typecheck, and tests; justify inapplicable checks. Consult [CI](references/pipeline.md) only when requested. Report corrections, evidence, and outstanding work.

## Good and weak tests

### Unit: free shipping from 100

Contract: shipping costs 10 below 100 and zero from 100.

```python
def shipping_cost(total):
    return 0 if total >= 100 else 10

def test_shipping_weak():
    assert shipping_cost(120) == 0

def test_shipping_boundary():
    assert shipping_cost(99) == 10
    assert shipping_cost(100) == 0
    assert shipping_cost(101) == 0
```

**Gap:** 120 cannot distinguish `>=` from `>`. **Correction:** observe 100 and its neighbors. **Proof:** temporarily replace `>=` with `>`; the weak test passes, the corrected test fails at 100. Restore and both pass. A test without assertions does not observe the return value.

### Integration: persistence through an independent connection

Adapt to the real API. The `db_path` fixture prepares an empty table in a disposable SQLite database; `create_order` returns the ID after writing and committing.

```python
import sqlite3
from contextlib import closing

def test_order_weak(db_path):
    assert create_order(db_path, "o-1") == "o-1"

def test_order_persisted(db_path):
    assert create_order(db_path, "o-1") == "o-1"
    with closing(sqlite3.connect(db_path)) as reader:
        saved = reader.execute(
            "SELECT id FROM orders WHERE id = ?", ("o-1",)
        ).fetchone()
        assert saved == ("o-1",)
```

**Gap:** returning the correct ID does not guarantee persistence. **Correction:** read through an independent connection after commit. **Proof:** omit INSERT or commit while keeping the return; the weak test passes, the corrected test fails on the missing record. Restore and confirm passage. Cache, repository mocks, and the same transaction cannot prove durability.

## Interpret tools

| Result | Decision |
| --- | --- |
| Relevant survivor | Tests allow a contract violation; close the gap and demonstrate detection. |
| Justified equivalent | Identical effects across the valid domain; document proof and domain evidence without artificial tests. |
| Necessist defect | Both removal and contractual regression pass; correct observation/setup and reproduce both. |
| Legitimate redundancy | Remaining observations detect the regression despite removal; preserve protection and necessary resource cleanup when simplifying. |
| Inconclusive | Error, timeout, invalid baseline, empty collection, or unproven cause; investigate without approving effectiveness. |

Accepting equivalence or redundancy resolves that finding, not the entire audit.

## Rationalizations and warning signs

| Rationalization | Response |
| --- | --- |
| "The suite is green." | Demonstrate the expected regression. |
| "The score is high." | Investigate survivors; avoid universal thresholds and metric-driven exclusions. |
| "The tool exited zero." | Check collection, categories, and reproduced findings. |
| "Reading tests equals execution." | Identify manual review and unexecuted analyses. |

Stop when metrics replace evidence, every removal becomes a defect, or correct legacy code must be rewritten.

## Completion checklist

- [ ] Verified, stable baseline with collected tests.
- [ ] Relevant contracts observed, including integration effects.
- [ ] Regressions demonstrated and correct behavior restored.
- [ ] Permanent corrections applied to the original; otherwise patch application explicitly pending.
- [ ] Findings classified with rationale; missing analyses identified.
- [ ] Final checks executed or inapplicability justified.
- [ ] Report distinguishes execution, manual review, and outstanding work; does not promise freedom from bugs.

## When blocked

Ambiguous contract: establish authority before choosing expectations; implementation cannot settle ambiguity.

Failing/unstable baseline: investigate isolation and cause; rerun before interpreting analyses.

Missing tool: verify support; try Docker with a reachable daemon and the project's test runtime before proposing host installation, within existing authorization. Report concrete blockers. Incompatible: record version/backend and alternatives; avoid framework migration.

Empty collection: check discovery, filters, and configuration. Zero candidates do not imply zero collected tests or prove effectiveness.

Read-only original: deliver a verified patch and mark application pending; do not claim corrections applied or the task complete.

Without execution, report limited manual review. Required but unexecuted tools prevent full approval.
