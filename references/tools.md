# Select and configure tools

Read for tool selection/configuration. Prefer adopted tools; verify language, framework, and versions using local configuration, installed `--help`, and official documentation.

| Stack | Candidates subject to compatibility |
| --- | --- |
| JS/TS | [StrykerJS](https://stryker-mutator.io/docs/stryker-js/introduction/) |
| PHP | [Infection](https://infection.github.io/guide/) |
| Rust | [cargo-mutants](https://mutants.rs/) |
| JVM | [PIT](https://pitest.org/) |
| Python | mutmut or Cosmic Ray |
| C/C++ | [Mull](https://mull.readthedocs.io/) |

For [Necessist](https://github.com/trailofbits/necessist), confirm backend/version; sample before expanding. Preserve native categories/reports and state score denominators. Timeouts are not assertion kills; zero candidates do not prove effectiveness.

`necessist --dump` queries stored results; historical queries are not fresh execution. Check available options; invalidate database/cache when revision/configuration changes. Do not assume JSON export or exit-code gates.
