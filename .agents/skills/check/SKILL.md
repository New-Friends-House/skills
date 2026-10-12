---
name: check
description: Run fix → typecheck → Vault suite, including diagnostic repairs.
disable-model-invocation: true
---

# Check

On explicit `/check` or `$check`, run the three gates below at the repository root, including diagnostic repairs. The user's invocation authorizes this run; do not ask again. A request to write or edit this skill does not execute the gates.

## Gates and completion

1. Run `pnpm run fix`; advance only when it exits 0.
2. Run `pnpm run typecheck`; advance only when it exits 0.
3. Read and follow [Vault E2E](../vault-e2e/SKILL.md) with the **main suite already selected**, then run `pnpm test:vault:suite`; success requires exit 0 for the full suite. Apply its preparation, failure diagnosis and repair, isolated reruns, cleanup, and lessons workflow. This invocation selects the main suite, so omit the mode question and Web E2E.

On failure, repair the reported errors and rerun the failed gate. Any code, test, or configuration edit after the fix gate, including suite repairs or concurrent changes, restarts the full sequence from `pnpm run fix`. Completion requires all three gates to pass in order on unchanged source. Wait for each process to exit; partial output is not a result.

For fix/typecheck, stop if a diagnostic survives three repair-and-rerun cycles. For the suite, follow Vault E2E's repair-and-rerun completion rule. If an environment, credential, or tool blocker prevents progress, report the concrete blocker without inventing configuration values. A blocked run is incomplete, not a passing check.

## Change scope

- Record the starting worktree changes so existing work remains distinguishable from gate fixes.
- Change only what diagnostics require. Preserve unrelated work; a diagnostic naming an already-dirty file permits only the needed repair.
- Repo-wide formatting changes made by `pnpm run fix` are expected and should remain.
- This invocation does not authorize commits, new test files, tests outside the main Vault suite, build/browser/device runs, or unrelated refactors.

Report each gate's final exit code (or that it did not run), files changed, unresolved diagnostics, and suite results with report paths. Passing these gates provides static and Vault/API behavioral evidence; Web UI and mobile behavior remain unverified.
