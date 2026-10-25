---
name: "prompt-version-change"
description: "Use when changing any LLM prompt: create a new version file, guard against eval leakage, keep golden parity, measure cheaply, and cap the number of iterations."
---

# Prompt version change

## Rules
- Never edit a committed prompt file. Copy it to the next version (`name_vN+1.md`), change only what is needed, and switch the active version in config.
- Add a test that pins the diff (exactly which lines changed vs the previous version) and, where a prompt must equal another (e.g. worker = answer prompt), a test that pins them equal.
- Examples in prompts must never be eval or golden questions, verbatim or near-verbatim. The all-prompts eval-wording test must pass; when replacing an example that test flags, rewrite the example rather than loosening the threshold.
- A prompt rule should state the principle (e.g. "split only when parts need different KB entries"), with one or two examples that are not eval items.

## Measuring
1. Mock tests and golden parity first (free): single-question behaviour must be unchanged unless the change is meant to alter it.
2. Then the cheapest live check that isolates the prompt (e.g. an orchestrator-only planning check), metered.
3. Then the full metered rerun of every eval the prompt affects. Skip evals whose path does not use the prompt.
4. Live results vary run to run. Before blaming a prompt, compare traces for the same question across runs; one rerun separates variance from a real regression. Report every run honestly.

## Iteration cap
- Decide the cap before starting (e.g. two versions). If the cap is hit, compare the versions on actual results, pick one, and record the remaining misses as known limitations. No further versions without explicit approval.
- Never revert to an older version that leaks eval wording just because its numbers looked better.