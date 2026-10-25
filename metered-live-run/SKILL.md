---
name: "metered-live-run"
description: "Use before any paid LLM/API run (evals, Ragas, sweeps, live checks): estimate first, enforce ledger + 2x ceiling + watchdog + stop rules, then report actual vs estimate."
---

# Metered live run

Every run that spends real money follows this protocol. No exceptions for "small" runs.

## Before spending
1. Build and test the run in mock mode first ($0). Commit it through the pre-commit hook.
2. Produce an estimate table: one row per run with calls, input/output tokens, $ estimate, and ceiling (2x estimate). Base estimates on measured per-call averages from the spend ledger when available, not guesses.
3. Show current ledger total and estimated remaining balance.
4. STOP and wait for an explicit go from the user.

## Run controls (all required)
- Every paid call is recorded in the persistent spend ledger (fixed path, never a temp data dir), including on crash.
- Spend ceiling at 2x the estimate, checked before each item, enforced even if raised inside a worker thread.
- Stall watchdog (e.g. 5 min with no progress -> abort, save checkpoint).
- Per-call timeout and capped retries; stop immediately on non-retryable errors (no credits, bad key).
- Resumable: checkpoint progress so a rerun resumes instead of restarting; refuse to resume if prompts/models/config changed.
- Long multi-step batches live in ONE driver script with all steps and stop rules inside it, plus --resume, so a context reset loses nothing.
- Stop rules are explicit quality gates (e.g. gap pass < 100%, safety leakage > 0, answer rate below floor). When one fires, stop the batch and report; do not continue or tweak and retry silently.

## After the run
- Report results plus ledger cost per run vs estimate, total, and remaining balance.
- Never print results from files older than the current batch; show "not run" instead.
- Commit result files only for the final, accepted run.
- If the ledger and the provider billing page disagree, investigate before the next run.