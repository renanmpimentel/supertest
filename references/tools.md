# Select and configure tools

Prefer adopted tools; verify compatibility/options through configuration, installed `--help`, and official documentation.

| Stack | Candidates |
| --- | --- |
| JS/TS | [StrykerJS](https://stryker-mutator.io/docs/stryker-js/introduction/) |
| .NET | [Stryker.NET](https://stryker-mutator.io/docs/stryker-net/introduction/) |
| PHP | [Infection](https://infection.github.io/guide/) |
| Rust | [cargo-mutants](https://mutants.rs/) |
| JVM | [PIT](https://pitest.org/) |
| Python | [mutmut](https://mutmut.readthedocs.io/) or [Cosmic Ray](https://cosmic-ray.readthedocs.io/) |
| Go | [Gremlins](https://github.com/go-gremlins/gremlins) or go-mutesting |
| Ruby | [mutant](https://github.com/mbj/mutant) |
| Swift | [Muter](https://github.com/muter-mutation-testing/muter) |
| C/C++ | [Mull](https://mull.readthedocs.io/) |

Check each tool's mutator list. Tools without statement-removal or branch-addition mutators give contracts such as "ignores X" or "does not fall back to X" no mutants; hand-write those contractual regressions. Tools that never shift a limit by one unit leave documented limits unprobed; the boundary probes in SKILL.md cover them.

## Necessist

[Necessist](https://github.com/trailofbits/necessist) frameworks (`--framework`): anchor, foundry, go, hardhat, php, python (pytest), rust and vitest; confirm with the installed `--help`, then sample first. Unsupported runner (for example, mocha or jest): remove the selected tests' statements manually, one at a time, and label results manual; never migrate frameworks. Skip assertions (`expect`, `assert*`), local assertion closures (for example, `check := func…`), and whole subtest blocks (for example, `t.Run`) before executing; their removal always passes.

Before running Necessist, search the test files for assertion helpers: functions whose body fails the test (for example, calls `t.Fatal`/`t.Error`, `assert`, or `expect`), such as `assertEqual` or `assertNoError`. Put every helper name found under `ignored_functions` (or `ignored_methods`) in `necessist.toml` (`--default-config` creates it) and record the search command. Unlisted helper removals report uninformative `passed` results: removing an assertion always passes.

`necessist --dump` reads history, not fresh execution. Check options; invalidate database/cache after revision/configuration changes. Never assume JSON export or exit-code gates.

## Known blind spots by tool

- **StrykerJS:** skips removal of brace-less one-line guards; hand-remove security guards.
- **Gremlins:** has no statement-removal or branch-addition mutators and never changes literals or drops conditions, so documented limits stay unprobed; for each min/max, hand-write the "limit removed" regression and add a `max+1` (or `min-1`) case. It runs only the mutated package's tests; confirm survivors with `go test ./...`.
- **mutmut 3:** reruns pytest in-process, so module-level state breaks it; scope with `do_not_mutate`.
- **All tools:** batch-classify mutants in documentation strings and HTTP header-name casing as equivalent.

## Running tools

Preserve native categories/reports and score denominators. Timeouts are not assertion kills; zero candidates prove nothing.

Mutation tools leave sandboxes inside the project (for example, `.stryker-tmp`, `mutants.out`) that test runners may collect, running copied tests against unmutated sources. Delete them or keep them outside the project, and recheck the collected count against the baseline before running any regression.

Missing locally: verify Docker daemon access; use compatible tool/test-runtime images with OS dependencies; record versions/digests. Mount the isolated copy writable, install compatible dependencies, configure test services, and verify its baseline. Restore files; retain results; clean up owned resources.
