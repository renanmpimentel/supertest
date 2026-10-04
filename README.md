# Supertest

An agent skill for auditing existing unit and integration tests against the contracts they should protect.

A passing suite is a starting point. Supertest asks for evidence that a test detects the specific defect it is meant to catch, then verifies that correct behavior passes again.

## When to use it

- Review tests that pass without observing meaningful outcomes.
- Find missing boundary cases, circular expectations, or mocks that hide required effects.
- Verify persistence, transactions, and other integration effects.
- Investigate application mutation survivors and Necessist findings.

The skill preserves correct legacy code and focuses corrections on demonstrated gaps.

## Install or load

Supertest uses the open [Agent Skills format](https://agentskills.io/specification).

With an agent that supports skills, download or clone this repository and place the `supertest/` directory in the skills location documented by that agent. Keep `SKILL.md` and `references/` together, along with the README and license. Installation paths, discovery, and invocation syntax depend on the agent.

If your environment does not load skills, provide `SKILL.md` as context and make its linked references available when requested. Ask the model to follow the Supertest workflow using the prompts below.

For a full audit, the agent needs access to the target project's source, contracts, test runner, and verification commands. It checks write access, required services, and connection settings before execution. Mutation tools are installed and configured separately for the target stack. Without command execution, the result is a limited manual review.

## Use it

Ask your agent to read the skill instructions and follow the workflow:

```text
Read SKILL.md and follow the Supertest workflow to audit the existing
unit tests for shipping costs.
Verify boundary behavior and demonstrate the regressions each corrected test catches.
```

```text
Read SKILL.md and follow the Supertest workflow to audit order persistence.
Verify committed data through an independent connection, beyond the response status.
```

```text
Read SKILL.md and follow the Supertest workflow to investigate
mutation survivors and Necessist findings
in the affected module. Report reproduced findings and execution limitations.
```

Use your agent's native skill invocation when available. Automatic discovery depends on the agent's support for skills. The portable format does not establish compatibility with every host or model; validate discovery and execution in your environment.

## What the audit delivers

The agent discovers and runs a stable baseline, audits unit and integration observations, evaluates compatible tools, and applies the smallest permanent test corrections to the original project. Temporary regressions run in an isolated copy containing relevant local changes, with correct behavior restored afterward.

The report identifies scope, commands, test and candidate counts, corrections, demonstrated regressions, classified findings, limitations, and outstanding work. It distinguishes actual execution from manual review and applied corrections from pending patches. If the original is read-only, the agent delivers a verified patch and reports its application as pending.

Tool availability and framework support constrain what can run. High scores, zero exit codes, and zero candidates alone do not establish test effectiveness. Timeouts are reported separately from assertion kills; passing removals do not justify discarding necessary cleanup. Mental mutation guides regression selection; it does not replace execution. CI integration is handled only when requested.

## Package contents

| Resource | Purpose |
| --- | --- |
| [Skill instructions](SKILL.md) | Audit workflow, examples, classifications, and completion criteria |
| [Good tests](references/good-tests.md) | Independent expectations, observable contracts, mocks, spies, and helpers |
| [Tools](references/tools.md) | Mutation tool candidates and Necessist guidance |
| [Optional CI](references/pipeline.md) | CI selection, artifacts, and gate verification |

## Credits and license

The audit workflow and good-test guidance draw on the [Superpowers TDD skill](https://github.com/obra/superpowers/tree/main/skills/test-driven-development) and [Writing Good Tests](https://github.com/obra/superpowers/blob/main/skills/test-driven-development/writing-good-tests.md) by Jesse Vincent, adapted here for auditing existing tests.

Released under the [MIT License](LICENSE). The license retains the copyright notice for material adapted from Superpowers.
