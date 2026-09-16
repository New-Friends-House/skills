---
name: check
description: Run the fix → typecheck gates when the user explicitly invokes /check or $check.
disable-model-invocation: true
---

# Check

On explicit `/check` or `$check`, run the two gates below at the repository root, including diagnostic repairs. The user's invocation authorizes this run; do not ask again.

## Gates and completion

1. Run `pnpm run fix`; advance only when it exits 0.
2. Run `pnpm run typecheck`; success requires exit 0 after the successful fix gate, with no intervening manual edits.

On failure, repair the reported errors and rerun the failed gate. Any manual code edit after the fix gate restarts the sequence from `pnpm run fix`. Wait for each process to exit; partial output is not a result.

Stop if a diagnostic survives three repair-and-rerun cycles, or an environment, credential, or tool blocker prevents progress. Report the blocker without inventing configuration values. A blocked run is incomplete, not a passing check.

## Change scope

- Record the starting worktree changes so existing work remains distinguishable from gate fixes.
- Change only what diagnostics require. Preserve unrelated work; a diagnostic naming an already-dirty file permits only the needed repair.
- Repo-wide formatting changes made by `pnpm run fix` are expected and should remain.
- This invocation does not authorize commits, new test files, extra test/build/browser/device runs, or unrelated refactors.

Report each gate's final exit code (or that it did not run), files changed, and unresolved diagnostics. Passing these gates is static evidence only.
