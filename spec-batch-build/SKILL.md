---
name: "spec-batch-build"
description: "Use when building a batch of tasks from SPEC/PLAN/TASKS: read the specs, build test-first with one commit per task through the hook, verify with smoke tests, update docs, and stop at the batch end."
---

# Spec-driven batch build

## Before coding
1. Read CLAUDE.md, the relevant SPEC sections, the TASKS rows for the batch, and the latest PLAN sections.
2. For a large or risky change, plan first (plan mode): new design, how every existing guarantee survives, which tests change and why, risks. Surface spec/code conflicts as questions; do not resolve them silently.
3. For refactors, record a golden snapshot of current behaviour first and add a parity test that must pass after every task.

## While building
- Test-first: write the failing test, then the code.
- One commit per task, citing FR ids, always through the pre-commit hook (pytest plus the real-process smoke test). Never use --no-verify.
- Definition of done: unit tests AND the end-to-end smoke test pass. Unit tests in a single-thread test runner are not enough for UI or agent-flow changes.
- Never create or edit repo files while a commit hook is running; draft in the scratchpad.
- Never run git checkout/restore/reset on files with uncommitted work; use stash or a worktree for mutation tests.
- Mock mode only; no paid calls unless the batch explicitly includes a metered run.
- SPEC.md is changed only with the user's explicit approval of the exact wording.
- Update TASKS.md and PLAN.md per task. Mark partially done tasks honestly (e.g. "built; live run deferred"), never fully ticked.

## At the batch end
- Stop and report: commits, test counts, smoke result, parity result, decisions needed, risks found, and anything deferred.
- Do not start the next batch without a go.