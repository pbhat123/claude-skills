---
name: "phase-release"
description: "Use at the end of a project phase: final metered reruns, results table into the docs, handoff note, git tag, then a clean context reset."
---

# Phase release

## 1. Final numbers
- Run every eval the phase could affect, through the batch driver with stop rules, ledger, ceilings and resume (see metered-live-run). Combine reruns with any pending ones (e.g. a drift point) so nothing is paid for twice.
- If a stop rule fires, triage first (see eval-failure-triage). Never change a stop rule without approval.

## 2. Results into the docs
- One final table: each metric now vs the previous phase, with every run shown when there were several, ledger cost per run, total and remaining balance.
- Write it into PLAN (results section), TASKS (rows ticked honestly; deferred live runs marked as such), and the evals README.
- Add known limitations and their causes to the README notes.

## 3. UI check
- Run the smoke test and the manual browser check (see ui-live-check) for anything user-facing that changed.

## 4. Handoff
- Add a short Handoff section to PLAN: current state, active versions (prompts, thresholds, models), final numbers, known limitations, deferred items, next step.
- Add any new working rule to CLAUDE.md.
- Commit through the hook; tree clean; nothing pushed unless asked.

## 5. Tag and reset
- `git tag phaseN-complete` on the final commit (tag a specific commit id if work continues meanwhile).
- Then `/clear`, and start the next phase with: "Read CLAUDE.md and the Handoff section of PLAN.md first."