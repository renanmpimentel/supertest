---
name: supertest
description: Use when creating, changing, running, or auditing unit and integration tests, investigating tests that pass without observing outcomes, or analyzing application mutations and Necessist findings.
license: MIT
metadata:
  version: 0.2.0
---

# Supertest

## Core rule

**Approve effectiveness only after observing detection of the expected defect.** Green establishes baseline.

Use temporary regressions in an isolated copy containing relevant local changes; restore and pass. Preserve correct legacy code. Collection/import/compilation/environment errors never prove detection.

**Isolated copy:** in a git repository, create it with `git worktree add` on a new temporary path, then copy any uncommitted changes the audit depends on; otherwise copy the project into a new directory from `mktemp -d`. To reset, create a fresh copy instead of deleting and recreating one with `rm -rf` (permission rules often block it). Remove your worktree with `git worktree remove` when done. The original project stays untouched until permanent corrections are applied.

## Scope routing

Load before test work; honor scope:

- **Run only:** execute the requested suite; report command, collection, passed/failed/skipped counts and limitations. Do not audit, mutate, or modify.
- **Create/change tests:** follow focused guidance; no automatic mutation/Necessist audit.
- **Audit:** default to progressive scope and evidence below.
- **Full audit:** retain the entire requested scope, audit cycle, compatible mutation/Necessist analyses, and completion checklist.

Internal baseline, regression, restoration, and final runs remain one invocation; never retrigger Supertest.

## Create or change tests

Establish authoritative contracts and independent expectations; cover boundaries/errors and integration effects. Consult [good tests](references/good-tests.md).

Existing correct behavior: pass, demonstrate expected assertion failure through isolated regression, restore, pass. Missing behavior: demonstrate expected assertion failure before authorized implementation; verify the same contract test passes afterward.

Run affected verification and required project checks; report changes, commands/counts, evidence, limitations, pending work. Failed checks remain pending; prevent completion.

## Audit cycle

### 1. Select and establish baseline

Read instructions, contracts, diff, stack, commands. Select changed/requested contracts plus unchanged protective tests/callers. Expand for shared dependencies, integration risk, unexpected failures, or unresolved findings. Unreliable mapping requires at least module scope. Explicit full audits retain all requested scope.

Match the CI runtime (versions, network); verify write access, services, connections. Record command, scope, collected/passed/failed/skipped counts from the runner's native summary, not wrappers; require stable, collected baseline before tool interpretation.

Reuse baseline only within this invocation with unchanged command, source/tests, dependencies, configuration, runtime. Never transfer baseline across unverified environments. Changes invalidate affected evidence/tool caches.

### 2. Audit unit tests

Check rules, boundaries, invalid inputs, errors, outcomes. Derive expectations without copying algorithms; avoid tautologies/core mocks, replace necessary boundaries. Consult [good tests](references/good-tests.md). Await promises; prove callbacks execute.

### 3. Audit integration tests

Observe real communication, persistence, transactions, effects; HTTP 201 alone cannot prove a write. Isolate data/services; deterministic setup, failure-safe cleanup, order independence (reverse tests sharing module state).

### 4. Evaluate effectiveness

Progressive audits add tool probes when findings/risk require; demonstrated local detection does not automatically require both tools. Explicit full audits run compatible application mutation testing and Necessist. Consult [tools](references/tools.md).

**Boundary probes (always, in every audit):** for each comparison against a limit in the audited code (a constant, configuration value or contract number used with `<`, `<=`, `>` or `>=`), run two regressions against the unmodified tests: shift the limit one unit down and one unit up (for example, `value < LIMIT` becomes `value < LIMIT - 1`, then `value < LIMIT + 1`). A shift that gives the same result for every valid input is equivalent; record it and continue. A surviving shift that changes some valid result is a finding: the tests check the limit only exactly at it or far from it. List every surviving shift in the report. Other hand-picked regressions do not replace these probes.

**Gap-pattern probes (always, in every audit):** go through every pattern in [good tests](references/good-tests.md) and decide whether the audited code has its trigger: a fallback or default, an input that must be ignored, an earlier guard, a later operation that repairs state, a fixture equal to what a regression would produce, a strict-to-lenient chain, a helper that receives the subject, a dependency that must not be called, a limit, a reference behavior to match. For each pattern that applies, execute the contractual regression it describes against the unmodified tests. Record which patterns applied, each regression and its outcome, and why the others do not apply. Finding one gap does not end the audit: keep probing the remaining applicable patterns within the budget and report every surviving regression.

Run analyses one at a time on the same isolated files, restoring them between analyses. Record tool versions, commands, scope, collected tests and candidates. Reproduce every survivor and every Necessist `passed` candidate before classifying it as a finding or a limitation. Reading code is not execution.

Necessist `passed` means the test still passed after that statement was removed: a weakness candidate, never evidence of effectiveness. Triage each candidate in order:

1. **Removed assertion or assertion-helper call** (for example, `assertEqual`): removing an assertion always passes. Classify configuration noise; stop. See [tools](references/tools.md). If its arguments perform an action (for example, `assertNoError(t, tx.Commit())`), continue to step 2.
2. **Explain why removal passed:** check whether the statement repeats a framework default or other setup; verify any language or framework semantics you rely on with a minimal executed check.
3. **Name the contract** the removed statement serves; consult the gap patterns in [good tests](references/good-tests.md).
4. **Execute a contractual regression against the unmodified test:** a temporary production-code change violating that contract. Removal, or removal combined with the regression, proves nothing.
   - Unmodified test fails: legitimate redundancy; stop.
   - Unmodified test passes: Necessist defect; correct and reproduce both.

Select or report a finding only from the last outcome; reasoned or conceptual regressions remain unverified candidates.

The budget is the number of regressions executed and tool candidates triaged: 30 per progressive audit unless the user sets another; boundary probes count toward it. Explicit full audits have no default budget. When candidates exceed the budget, order files and survivors by contract risk and process them in that order until the budget ends: documented, security, validation, and decision-guard contracts first; timing-dependent tests last. Record unsampled files and untriaged survivors.

### 5. Correct and verify

By default, apply the smallest permanent test corrections to the original project. In the isolated copy, show that each corrected test fails under the regression and passes again once the code is restored; repeat the relevant Necessist removals.

If the unmodified production code already violates the contract, that is a production defect, not a test gap: show the contract test failing first, apply the smallest authorized fix, then show the same test passing.

Iterate with focused checks, and rerun whole suites only when risk or project rules require it. Before finishing, rerun the analyses already executed that your changes affect, and freshly run the original project's required lint, typecheck and tests; never substitute reused evidence. If a check does not apply, say why. Consult [CI](references/pipeline.md) only when requested.

Wait for every run you started, including background jobs, to finish before the final report; never end with checks still running.

Report corrections/evidence/pending work, selected/excluded scope, unexecuted analyses, and phase durations (setup, normal tests, mutations, Necessist, final checks). Progressive results cannot grant full-audit approval; claim no unmeasured speedup.

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

**Gap:** 120 misses `>=` versus `>`. **Correction:** observe 100 and neighbors. **Proof:** temporarily use `>`: weak passes, corrected fails at 100. Restore; both pass. No assertions means no return observation.

### Integration: persistence through an independent connection

Adapt API: `db_path` prepares an empty disposable SQLite table; `create_order` writes, commits, returns ID.

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

**Gap:** ID return cannot prove persistence. **Correction:** read independently after commit. **Proof:** omit INSERT/commit, retain return: weak passes, corrected fails on missing record. Restore and pass. Cache, repository mocks, same transaction cannot prove durability.

## Interpret tools

| Result | Decision |
| --- | --- |
| Relevant survivor | Contract violation allowed; correct and demonstrate detection. |
| Justified equivalent | Identical valid-domain effects; document proof/domain evidence, no artificial tests. |
| Necessist defect | Removal passes, and the unmodified test also passes the contractual regression; correct observation/setup, reproduce both. |
| Legitimate redundancy | Remaining observations detect regression despite removal; preserve protection/necessary cleanup. |
| Inconclusive | Error, timeout, invalid baseline, empty collection, unproven cause; investigate without approval. |

Equivalence/redundancy resolves only that finding.

## Rationalizations and warning signs

| Rationalization | Response |
| --- | --- |
| "Green suite/high score." | Prove regression; investigate survivors, avoid universal thresholds/metric exclusions. |
| "Zero exit." | Check collection/categories/reproduced findings. |
| "Reading equals execution." | Disclose manual review/unexecuted analyses. |

Stop when metrics replace evidence, every removal becomes defective, or correct legacy code is rewritten.

## Full-audit completion checklist

- [ ] Verified, stable collected baseline.
- [ ] Contracts observed, including integration effects.
- [ ] Regressions demonstrated; correct behavior restored.
- [ ] Corrections applied to original; otherwise patch application explicitly pending.
- [ ] Findings classified with rationale; missing analyses identified.
- [ ] Final checks executed or inapplicability justified.
- [ ] Report separates execution/manual review/outstanding work; does not promise freedom from bugs.

## When blocked

Ambiguous contract: establish authority; implementation cannot settle ambiguity.

Unstable/failing baseline: investigate isolation/cause; rerun before interpretation.

Missing required tool: verify support; try Docker with reachable daemon and project test runtime before host installation proposals, within authorization. Report blockers. Incompatible: record version/backend/alternatives; avoid framework migration.

Empty collection: check discovery/filters/configuration. Zero candidates imply neither zero tests nor effectiveness.

Read-only original: deliver verified patch; application pending, never applied/complete.

Without execution, report limited manual review. Required unexecuted tools prevent full approval.
