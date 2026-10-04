---
name: supertest
description: Use when creating, changing, running, or auditing unit and integration tests, investigating tests that pass without observing outcomes, or analyzing application mutations and Necessist findings.
---

# Supertest

## Overview

Protect contracts through tests; report evidence and limitations.

## Core rule

**Approve effectiveness only after observing detection of the expected defect.** Green establishes baseline, not effectiveness.

Use temporary regressions in an isolated copy containing relevant local changes; restore afterward. Preserve correct legacy code; never delete or rewrite it for this proof. Collection, import, compilation, or environment errors do not prove detection.

## Scope routing

Load before test work; honor scope:

- **Run only:** execute the requested suite; report command, collection, passed/failed/skipped counts and limitations. Do not audit, mutate, or modify.
- **Create/change tests:** follow focused guidance below, without automatically expanding into mutation/Necessist audits.
- **Full audit:** follow the entire audit cycle and completion checklist.

Baseline, regression, restoration, and final runs remain one invocation; they do not retrigger Supertest.

## Create or change tests

Establish an authoritative contract before deriving independent expectations. Cover required boundaries/errors and observable integration effects; consult [good tests](references/good-tests.md).

For existing correct behavior, pass first; prove expected assertion failure with an isolated temporary regression, restore, and pass. For missing new behavior, prove expected assertion failure before authorized implementation, then verify the same contract test passes. Setup errors are not proof.

Run affected verification and required project checks; report changes, commands/counts, failure/restoration evidence, limitations, pending work. Failed checks remain pending; prevent completion.

## Audit cycle

### 1. Discover and run

Read instructions, contracts, diff, stack, and commands; include unchanged protective tests. Verify write access, services, and all test connection settings before execution.

Record suite command, scope, collected/passed/failed/skipped counts. Require a verified, stable baseline with actual collection before interpreting tools.

### 2. Audit unit tests

Check rules, boundaries, invalid inputs, errors, and outcomes. Derive contractual expectations without copying algorithms. Avoid tautologies and core mocks; replace necessary boundaries. Consult [good tests](references/good-tests.md).

Await promises; prove callbacks execute. Uncalled assertions protect nothing.

### 3. Audit integration tests

Exercise real communication, persistence, transactions, and effects. HTTP 201 does not prove a write.

Isolate data/services, make setup deterministic, clean up after failures, and check order independence.

### 4. Evaluate effectiveness

Run compatible application mutation testing and Necessist; consult [tools](references/tools.md).

Run tools sequentially on the same isolated files; restore between analyses. Record versions, commands, scope, collected tests, candidates, reports. Reproduce survivors and `passed` candidates; classify findings/limitations. Manual review is not execution.

### 5. Correct and verify

Apply minimal permanent test corrections to the original project by default. Show expected regression failure and restored passage in isolation. Repeat relevant removals for Necessist defects.

Rerun affected analyses and the original project's required lint, typecheck, tests; justify inapplicability. Consult [CI](references/pipeline.md) only when requested. Report corrections, evidence, outstanding work.

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

**Gap:** 120 cannot distinguish `>=` from `>`. **Correction:** observe 100 and neighbors. **Proof:** replace `>=` with `>` temporarily: weak passes, corrected fails at 100. Restore; both pass. No assertions means no return observation.

### Integration: persistence through an independent connection

Adapt to the real API. `db_path` prepares an empty table in disposable SQLite; `create_order` writes, commits, and returns the ID.

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

**Gap:** ID return does not guarantee persistence. **Correction:** read independently after commit. **Proof:** omit INSERT or commit, retaining return: weak passes, corrected fails on missing record. Restore and pass. Cache, repository mocks, or the same transaction cannot prove durability.

## Interpret tools

| Result | Decision |
| --- | --- |
| Relevant survivor | Contract violation allowed; close gap and demonstrate detection. |
| Justified equivalent | Identical effects throughout valid domain; document proof/domain evidence without artificial tests. |
| Necessist defect | Removal and contractual regression both pass; correct observation/setup and reproduce both. |
| Legitimate redundancy | Remaining observations detect regression despite removal; preserve protection and necessary cleanup. |
| Inconclusive | Error, timeout, invalid baseline, empty collection, unproven cause; investigate without approval. |

Equivalence/redundancy resolves only that finding.

## Rationalizations and warning signs

| Rationalization | Response |
| --- | --- |
| "The suite is green." | Demonstrate the expected regression. |
| "The score is high." | Investigate survivors; avoid universal thresholds and metric-driven exclusions. |
| "The tool exited zero." | Check collection, categories, and reproduced findings. |
| "Reading tests equals execution." | Identify manual review and unexecuted analyses. |

Stop for metrics replacing evidence, every removal labeled defective, or rewriting correct legacy code.

## Full-audit completion checklist

- [ ] Verified, stable baseline with collected tests.
- [ ] Relevant contracts observed, including integration effects.
- [ ] Regressions demonstrated and correct behavior restored.
- [ ] Corrections applied to original; otherwise patch application explicitly pending.
- [ ] Findings classified with rationale; missing analyses identified.
- [ ] Final checks executed or inapplicability justified.
- [ ] Report distinguishes execution, manual review, outstanding work; does not promise freedom from bugs.

## When blocked

Ambiguous contract: establish authority; implementation cannot settle ambiguity.

Failing/unstable baseline: investigate isolation/cause; rerun before interpretation.

Missing tool: verify support; try Docker with reachable daemon and project test runtime before host installation proposals, within existing authorization. Report concrete blockers. Incompatible: record version/backend, alternatives; avoid framework migration.

Empty collection: check discovery, filters, configuration. Zero candidates imply neither zero tests nor effectiveness.

Read-only original: deliver verified patch; mark application pending, never corrections applied or task complete.

Without execution, report limited manual review. Required but unexecuted tools prevent full approval.
