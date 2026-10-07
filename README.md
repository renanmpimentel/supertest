# Supertest

An agent skill for creating, changing, running, and auditing unit and integration tests against the contracts they should protect.

A passing suite is a starting point. Supertest asks for evidence that a test detects the specific defect it is meant to catch, then verifies that correct behavior passes again.

**Quick start (Claude Code):**

```bash
git clone https://github.com/renanmpimentel/supertest ~/.claude/skills/supertest
```

Then ask: `Load Supertest and audit the tests for <contract> progressively.` You get the gaps found, each proven by a temporary regression the old test missed and the corrected test catches.

## Example finding

A circuit-breaker suite passed 12/12. Its "open circuit returns the fallback" test expected `"Delayed service is down"` — the same string the real service throws. Supertest made the open circuit call the service anyway: still 12/12 green. The corrected test counts calls and gives each failure a distinct message (`Failure #1`, `Failure #2`), so the same regression now fails it. The production code was already correct; the test just could not tell.

## When to use it

- Create first tests for existing behavior or tests for a new feature.
- Change tests as contracts evolve, using independent expectations.
- Run a requested suite and report its results within that scope.
- Audit changed or requested contracts progressively, expanding for findings and risk.
- Request a full audit when the entire requested scope and compatible tool analyses are needed.
- Review tests that pass without observing meaningful outcomes.
- Find missing boundary cases, circular expectations, or mocks that hide required effects.
- Verify persistence, transactions, and other integration effects.
- Investigate mutation survivors (code changes no test noticed) and [Necessist](https://github.com/trailofbits/necessist) findings (test statements that can be removed while the test still passes).

The skill preserves correct legacy code and focuses corrections on demonstrated gaps.

## Install or load

Supertest uses the open [Agent Skills format](https://agentskills.io/specification).

With an agent that supports skills, clone this repository into a directory named `supertest` inside that agent's skills location (for Claude Code: `~/.claude/skills/supertest`, or `.claude/skills/supertest` in a project). Keep `SKILL.md` and `references/` together, along with the README and license. Installation paths, discovery, and invocation syntax depend on the agent.

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
contract. Demonstrate assertion failure with an isolated temporary regression,
then restore and pass.
```

```text
Load Supertest and run the order integration suite only.
Report the command, collection, passed/failed/skipped counts, and limitations.
```

```text
Load Supertest and audit the changed shipping contract progressively.
Include unchanged protective tests and callers; expand for findings or risks.
```

```text
Read SKILL.md and perform a full audit of order persistence, including
compatible mutation testing and Necessist. Verify committed data through an
independent connection, beyond the response status.
```

Use your agent's native skill invocation when available, and validate discovery and execution in your environment.

## How it works

- **Scope:** run-only, create/change, progressive audit (default: changed contracts plus the tests and callers protecting them, expanding on findings or risk) and explicit full audit (entire scope, mutation testing and Necessist, full checklist).
- **Evidence:** a stable, collected baseline first; every claimed gap is a temporary regression in an isolated copy that the old test misses and the corrected test catches, then restored. Collection, import and environment errors never count as proof.
- **Corrections:** smallest permanent test changes in the original project; correct legacy code is preserved. If current code violates the contract, it is reported as a production defect.
- **Report:** scope, commands, counts, demonstrated regressions, classified findings, unexecuted analyses, phase durations and pending work — separating execution from manual review. High scores, zero exits and zero candidates alone prove nothing.

The full rules live in [SKILL.md](SKILL.md).

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
