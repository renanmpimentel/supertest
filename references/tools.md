# Select and configure tools

Prefer adopted tools; verify compatibility/options through configuration, installed `--help`, and official documentation.

| Stack | Candidates |
| --- | --- |
| JS/TS | [StrykerJS](https://stryker-mutator.io/docs/stryker-js/introduction/) |
| PHP | [Infection](https://infection.github.io/guide/) |
| Rust | [cargo-mutants](https://mutants.rs/) |
| JVM | [PIT](https://pitest.org/) |
| Python | mutmut or Cosmic Ray |
| C/C++ | [Mull](https://mull.readthedocs.io/) |

Confirm [Necessist](https://github.com/trailofbits/necessist) backend/version; sample first. Preserve native categories/reports and score denominators. Timeouts are not assertion kills; zero candidates prove nothing.

Missing locally: verify Docker daemon access; use compatible tool/test-runtime images with OS dependencies; record versions/digests. Mount the isolated copy writable, install compatible dependencies, configure test services, and verify its baseline. Restore files; retain results; clean up owned resources.

`necessist --dump` reads history, not fresh execution. Check options; invalidate database/cache after revision/configuration changes. Never assume JSON export or exit-code gates.
