---
name: check
description: Run pnpm run fix, then typecheck, and fix until each gate passes. Use when the user invokes /check.
disable-model-invocation: true
---

# Check

`/check` **overrides** `AGENTS.md`'s ban on `pnpm run fix` / `pnpm run typecheck`. Run those commands here. Do not wait for the user to run them.

Work at the repository root. Size `block_until_ms` for a repo-wide Turbo run (several minutes). Wait until the process exits. Do not treat a partial log as success.

## Gates

Run in this order. A gate is **green** only on exit code 0.

1. `pnpm run fix`
2. `pnpm run typecheck`

## Loop

For the current gate:

1. Run the command.
2. Exit 0 → advance to the next gate.
3. Exit non-zero → read the diagnostics, fix the reported errors, re-run **the same command**.
4. A code edit made for typecheck **restarts from `pnpm run fix`**. Later edits can fail earlier gates.
5. The same diagnostic remaining after 3 fix-and-rerun cycles → stop. Report the leftover errors and the files already changed.

**Complete when:** both commands exit 0 in one uninterrupted pass (fix → typecheck, no edits between), or a named blocker remains.

## Fix scope

- Change only what the current command's diagnostics require.
- Leave unrelated worktree changes untouched unless a diagnostic names those files.
- `pnpm run fix` may rewrite formatting across the repo. That is expected; do not revert it.
- No commits. No new test files. No drive-by refactors.
- Missing env, credentials, or secrets → stop and report. Do not invent values.

## Report

- Each gate: pass or fail, with the last command's exit.
- Files changed to get green.
- Leftover diagnostics and which gate they belong to, if any.
