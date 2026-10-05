# Select and configure tools

Prefer adopted tools; verify compatibility/options through configuration, installed `--help`, and official documentation.

| Stack | Candidates |
| --- | --- |
| JS/TS | [StrykerJS](https://stryker-mutator.io/docs/stryker-js/introduction/) |
| PHP | [Infection](https://infection.github.io/guide/) |
| Rust | [cargo-mutants](https://mutants.rs/) |
| JVM | [PIT](https://pitest.org/) |
| Python | mutmut or Cosmic Ray |
| Go | [Gremlins](https://github.com/go-gremlins/gremlins) or go-mutesting |
| C/C++ | [Mull](https://mull.readthedocs.io/) |

Check each tool's mutator list. Without statement-removal or branch-addition mutators (for example, Gremlins), contracts such as "ignores X" or "does not fall back to X" receive no mutants; hand-write those contractual regressions.

Confirm [Necessist](https://github.com/trailofbits/necessist) backend/version against the project's runner (`--framework` values in `--help`); sample first. Unsupported runner (for example, mocha or jest): remove the selected tests' statements manually, one at a time, and label results manual; never migrate frameworks.

Before running Necessist, search the test files for assertion helpers: functions whose body fails the test (for example, calls `t.Fatal`/`t.Error`, `assert`, or `expect`), such as `assertEqual` or `assertNoError`. Put every helper name found under `ignored_functions` (or `ignored_methods`) in `necessist.toml` (`--default-config` creates it) and record the search command. Unlisted helper removals report uninformative `passed` results: removing an assertion always passes.

Preserve native categories/reports and score denominators. Timeouts are not assertion kills; zero candidates prove nothing.

Missing locally: verify Docker daemon access; use compatible tool/test-runtime images with OS dependencies; record versions/digests. Mount the isolated copy writable, install compatible dependencies, configure test services, and verify its baseline. Restore files; retain results; clean up owned resources.

`necessist --dump` reads history, not fresh execution. Check options; invalidate database/cache after revision/configuration changes. Never assume JSON export or exit-code gates.
