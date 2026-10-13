---
name: check
description: Run fix → typecheck, including diagnostic repairs.
disable-model-invocation: true
---

# Check

On explicit `/check` or `$check`, run the two gates below at the repository root, including diagnostic repairs. The user's invocation authorizes this run; do not ask again. A request to write or edit this skill does not execute the gates.

## Gates and completion

1. Run `pnpm run fix`; advance only when it exits 0.
2. Run `pnpm run typecheck`; advance only when it exits 0.

On failure, repair the reported errors and rerun the failed gate. Any code, test, or configuration edit after the fix gate, including concurrent changes, restarts the full sequence from `pnpm run fix`. Completion requires both gates to pass in order on unchanged source. Wait for each process to exit; partial output is not a result.

Stop if a diagnostic survives three repair-and-rerun cycles. If an environment, credential, or tool blocker prevents progress, report the concrete blocker without inventing configuration values. A blocked run is incomplete, not a passing check.

## Change scope

- Record the starting worktree changes so existing work remains distinguishable from gate fixes.
- Change only what diagnostics require. Preserve unrelated work; a diagnostic naming an already-dirty file permits only the needed repair.
- Repo-wide formatting changes made by `pnpm run fix` are expected and should remain.
- This invocation does not authorize commits, new test files, tests, builds, browser/device runs, or unrelated refactors.

Report each gate's final exit code (or that it did not run), files changed, and unresolved diagnostics. Passing these gates provides static evidence; runtime behavior remains unverified.
