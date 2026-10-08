# Optional CI

Apply only when requested. Reuse the project's commands, package manager and policies; do not introduce a new test runner.

## Select what runs

- Run the affected tests for changed application code; when the diff cannot guide selection, run the whole module.
- Run mutation testing and Necessist on the same selection, sequentially, on a clean checkout.
- Pin tool versions, runtime images and dependencies in the pipeline configuration, and invalidate caches after a revision or configuration change.

## Gates

Fail the pipeline when any of these holds:

- the baseline collects zero tests, or fewer than the previous run without an explained change;
- a tool reports an error, a timeout or an invalid baseline (these are not results);
- a known regression stops being detected (keep a small set of committed regressions and require each to fail the suite);
- a mutation survivor or Necessist `passed` candidate in a documented, security, validation or decision-guard contract is untriaged.

Do not gate on a universal mutation score; gate on the classified findings from SKILL.md.

## Artifacts

Keep, also on failure: the runner's native summary, tool reports with their denominators, tool versions or image digests, and the list of untriaged candidates.

## Claims

Claim the pipeline works only after a run in the target CI has executed and failed on a known regression. Merge enforcement depends on the provider's branch rules; say which rule is required.
