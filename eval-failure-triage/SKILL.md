---
name: "eval-failure-triage"
description: "Use when an eval, smoke test or stop rule fails: inspect saved results at no cost, classify each failure, and propose fixes without changing code or spending until approved."
---

# Eval failure triage

When something fails, diagnose before fixing, and diagnose for free.

## Steps
1. Do not rerun anything paid. Work only from saved results, traces, logs and cached embeddings.
2. For each failing case, show the evidence: inputs, intermediate decisions (routing, parts, retrieval scores, worker/judge verdicts, retries), and the final output.
3. Classify every failure as exactly one of:
   - real bug (code or prompt behaves wrongly)
   - outdated or too-strict check (the system is right, the test assumes old behaviour)
   - random model variance (recovered or flips between runs)
   - metric false positive (e.g. a judge flags a faithful paraphrase)
   - known limitation (document, don't fix)
4. Look for metric honesty problems: does the headline number count failures as successes? If so, report an honest companion metric next to it rather than redefining the old one.
5. Propose fixes with a test plan for each. Prefer fixes that cannot create a worse failure (e.g. merge-only guards over split guards; never anything that could answer a question the KB cannot).
6. Never relabel eval data or edit the KB to make a failing case pass (that tunes to the test). Record it as a known failure with its cause instead.
7. Stop and wait for approval before changing code or spending.

## Iteration limits
- Prompt fixes get a capped number of iterations. If the cap is hit, compare versions on actual results and pick the best; do not keep tweaking.
- If diagnostics were insufficient (missing per-step data), add the diagnostics first, then rerun only the affected cases.