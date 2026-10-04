# Supertest

An agent skill for creating, changing, running, and auditing unit and integration tests against the contracts they should protect.

A passing suite is a starting point. Supertest asks for evidence that a test detects the specific defect it is meant to catch, then verifies that correct behavior passes again.

## When to use it

- Create first tests for existing behavior or tests for a new feature.
- Change tests as contracts evolve, using independent expectations.
- Run a requested suite and report its results within that scope.
- Audit changed or requested contracts progressively, expanding for findings and risk.
- Request a full audit when the entire requested scope and compatible tool analyses are needed.
- Review tests that pass without observing meaningful outcomes.
- Find missing boundary cases, circular expectations, or mocks that hide required effects.
- Verify persistence, transactions, and other integration effects.
- Investigate application mutation survivors and Necessist findings.

The skill preserves correct legacy code and focuses corrections on demonstrated gaps.

## Install or load

Supertest uses the open [Agent Skills format](https://agentskills.io/specification).

With an agent that supports skills, download or clone this repository and place the `supertest/` directory in the skills location documented by that agent. Keep `SKILL.md` and `references/` together, along with the README and license. Installation paths, discovery, and invocation syntax depend on the agent.

If your environment does not load skills, provide `SKILL.md` as context and make its linked references available when requested. Ask the model to follow Supertest within the requested scope using the prompts below.

For a full audit, the agent needs access to the target project's source, contracts, test runner, and verification commands. It checks write access, required services, and connection settings before execution.

Mutation tools require a compatible version and environment for the target stack. If a tool required by the selected audit is absent locally, Supertest checks Docker and daemon access before proposing host installation. A progressive audit does not prepare a container merely because an unused tool is absent. Within existing execution authorization, it can prepare a disposable container containing the tool and the project's test runtime; Rust need not be installed on the host. Tests establish their baseline inside that environment, using isolated sources and test services. The report records concrete blockers if this route cannot run. Without command execution, the result is a limited manual review.

## Configure each target project

Install and load Supertest in the host, then add this rule to each target project's existing canonical agent-instruction file (for example, `AGENTS.md`):

```text
Load the installed Supertest skill before creating, changing, running, or
auditing unit or integration tests. Honor the requested scope: run-only
requests execute the requested suite and report results without auditing
or modifying tests; creation and changes use focused guidance. Audits default
to progressive scope, expanding for findings and shared or integration risks;
explicit full audits retain the entire requested scope and tool workflow.
Treat internal baseline, regression, restoration, and final verification runs
as one invocation; do not retrigger the skill for those runs.
```

This repository cannot automatically configure every project. Configure the rule in each target project and verify that its host loads the installed skill. Hosts without native skill support can include `SKILL.md` and its references as context alongside the same project rule.

Activation depends on the host's skill selection and project-instruction support. A direct shell-only `npm test` or `pytest` command does not launch an LLM or load Supertest. Actual test-command hooks require a separately configured executor; this package provides the skill and instruction rule.

## Use it

Ask your agent to load the skill and honor the requested scope:

```text
Load Supertest and create the first unit tests for the existing shipping-cost
contract. Preserve correct code and demonstrate assertion failure using an
isolated temporary regression, then restore and pass.
```

```text
Load Supertest and create integration tests for the new order-cancellation
contract. Demonstrate expected assertion failure for missing behavior before
authorized implementation, then verify the same contract tests pass. Report
failed final checks as pending; do not claim completion.
```

```text
Load Supertest and update the affected tests for the revised shipping contract.
Verify the boundary cases and report the affected checks.
```

```text
Load Supertest and run the order integration suite only.
Report the command, collection, passed/failed/skipped counts, and limitations.
```

Progressive audit example:

```text
Load Supertest and audit the changed shipping contract progressively.
Include unchanged protective tests and callers; expand for findings or risks.
Disclose selected and excluded scope and unexecuted analyses.
```

Full-audit examples:

```text
Read SKILL.md and perform a full audit of the existing unit tests for
shipping costs, including compatible mutation testing and Necessist.
Verify boundary behavior and demonstrate the regressions each corrected test catches.
```

```text
Read SKILL.md and perform a full audit of order persistence,
including compatible mutation testing and Necessist.
Verify committed data through an independent connection, beyond the response status.
```

```text
Read SKILL.md and perform a full audit of the affected module, investigating
mutation survivors and Necessist findings across the entire requested scope.
Report reproduced findings and execution limitations.
```

Use your agent's native skill invocation when available. Automatic discovery depends on the agent's support for skills. The portable format does not establish compatibility with every host or model; validate discovery and execution in your environment.

## Audit modes and execution evidence

Ordinary audits default to progressive scope: changed or requested contracts plus unchanged tests and callers that protect them. The agent expands for shared dependencies, integration risks, unexpected failures, or unresolved findings. If mapping is unreliable, it selects at least the module. The agent demonstrates contract regression detection and adds tool probes when findings or risk require them; proving a local defect does not automatically launch both mutation testing and Necessist. Previously executed affected analyses are rerun after changes; unresolved findings or risk can justify new analyses.

An explicit full audit retains the entire requested scope, compatible application mutation testing and Necessist, restoration, corrections, and the full completion checklist. Progressive reports disclose selected and excluded scope and unexecuted analyses; they cannot grant full-audit approval.

A collected baseline can be reused only within the same invocation when the command, source, tests, dependencies, configuration, runtime, and services are unchanged. Evidence from the original project cannot be transferred to an isolated copy or container without verifying that environment. Changes invalidate affected evidence and tool caches. Temporary regressions still require restoration and a passing check afterward.

During correction, the agent uses focused checks rather than repeating the whole suite per finding unless risk or project requirements demand it. Fresh required final lint, typecheck, and tests run in the original project; earlier green results cannot replace this gate. Run-only requests retain their exact requested suite.

The report records durations for setup, normal tests, mutations, Necessist, and final checks as applicable. These measurements support later comparisons; this package makes no measured speedup claim.

## What creation and audits deliver

Creation and changes start from an authoritative contract, cover required boundaries and errors, and observe integration effects. Tests for existing correct behavior demonstrate detection through isolated temporary regressions. For missing new behavior, the agent observes the expected assertion failure before authorized implementation, then verifies that the same contract tests pass. Collection, import, and environment errors do not count as that proof. The agent runs affected verification and required project checks and reports evidence, limitations, and pending work. Failed final checks remain pending and prevent a completion claim. Focused test work does not automatically expand into a full mutation or Necessist audit.

Run-only requests execute the requested suite and report results without auditing, mutating, or modifying tests.

For full audits, the agent discovers and runs a stable baseline, audits unit and integration observations, evaluates compatible tools, and applies the smallest permanent test corrections to the original project. Temporary regressions run in an isolated copy containing relevant local changes, with correct behavior restored afterward.

The report identifies selected and excluded scope, commands, test and candidate counts, corrections, demonstrated regressions, classified findings, unexecuted analyses, phase durations, limitations, and outstanding work. It distinguishes actual execution from manual review and applied corrections from pending patches. If the original is read-only, the agent delivers a verified patch and reports its application as pending.

Tool availability and framework support constrain what can run. High scores, zero exit codes, and zero candidates alone do not establish test effectiveness. Timeouts are reported separately from assertion kills; passing removals do not justify discarding necessary cleanup. Mental mutation guides regression selection; it does not replace execution. CI integration is handled only when requested.

## Package contents

| Resource | Purpose |
| --- | --- |
| [Skill instructions](SKILL.md) | Scope routing, creation guidance, progressive/full audits, examples, and completion criteria |
| [Good tests](references/good-tests.md) | Independent expectations, observable contracts, mocks, spies, and helpers |
| [Tools](references/tools.md) | Mutation tool candidates and Necessist guidance |
| [Optional CI](references/pipeline.md) | CI selection, artifacts, and gate verification |

## Credits and license

The audit workflow and good-test guidance draw on the [Superpowers TDD skill](https://github.com/obra/superpowers/tree/main/skills/test-driven-development) and [Writing Good Tests](https://github.com/obra/superpowers/blob/main/skills/test-driven-development/writing-good-tests.md) by Jesse Vincent, adapted here for creating and auditing tests.

Released under the [MIT License](LICENSE). The license retains the copyright notice for material adapted from Superpowers.
