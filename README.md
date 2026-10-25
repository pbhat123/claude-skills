# 🧰 Claude Code Skills for LLM Engineering

Seven reusable [Claude Code](https://claude.com/claude-code) skills for building LLM agents that are spec-driven, cost-controlled and eval-backed. They came out of building [SmartDesk AI](https://github.com/pbhat123/SmartDesk_AI), a multi-agent IT/HR help desk.

## ⚡ The skills

| Skill | Use it when | What it enforces |
|---|---|---|
| 🏗️ **spec-batch-build** | Building a batch of tasks from SPEC / PLAN / TASKS | Test-first, one commit per task through the pre-commit hook, smoke test, docs updated, stop at the batch end |
| 💰 **metered-live-run** | Before any paid LLM/API run (evals, Ragas, sweeps) | Estimate first, spend ledger, 2× ceiling, watchdog, stop rules, actual-vs-estimate report |
| 🔍 **eval-failure-triage** | An eval, smoke test or stop rule fails | Classify each failure from saved results at no cost; propose fixes, change nothing until approved |
| ✍️ **prompt-version-change** | Changing any LLM prompt | New version file (never edit in place), eval-leak guard, golden parity, cheap measurement, capped iterations |
| 🖥️ **ui-live-check** | After a UI or agent-flow change (Streamlit) | Smoke test, then a short manual browser check with a fixed checklist |
| 🏁 **phase-release** | Closing a project phase | Final metered reruns, results table in docs, handoff note, git tag, clean context reset |
| 🎛️ **model-effort-switch** | Changing Claude Code's model or effort level | Which model and effort fit the task, and how to set defaults |

## 🚀 Install

Copy the skill folders into your Claude Code skills directory:

```bash
git clone https://github.com/pbhat123/claude-skills.git
# all projects (personal skills)
cp -R claude-skills/*/ ~/.claude/skills/
# or one project only
cp -R claude-skills/*/ /path/to/project/.claude/skills/
```

Claude Code picks a skill up automatically when the task matches its description. You can also call one by name, for example `/metered-live-run`.

## 🪝 Bonus: pre-commit hook

`pre-commit` blocks a commit unless the tests pass. It runs the test suite on every commit, and a UI smoke test when UI files are staged. Each step is killed after a time limit, so a hung test can't freeze git. `spec-batch-build` relies on it.

```bash
cp pre-commit /path/to/project/.git/hooks/pre-commit
chmod +x /path/to/project/.git/hooks/pre-commit
```

| Setting (env var) | Default | What it is |
|---|---|---|
| `HOOK_TIMEOUT` | `600` | Seconds per step before it is stopped |
| `HOOK_TEST_CMD` | `pytest -q` | Test command |
| `HOOK_SMOKE_CMD` | `python scripts/smoke_ui.py` | UI smoke command |
| `HOOK_UI_PATTERN` | `(^\|/)ui/` | Staged paths that trigger the smoke test |

Rule: never bypass it with `git commit --no-verify`.

## 🧭 How they fit together

```
spec-batch-build ──► ui-live-check
        │
        ▼
prompt-version-change ──► metered-live-run ──► eval-failure-triage
                                  │
                                  ▼
                            phase-release
```

## 📁 Layout

```
<skill-name>/
└── SKILL.md   # frontmatter (name, description) + the steps
pre-commit     # git hook: tests (+ UI smoke) with a time limit
```

## 📝 Notes

- The skills assume a repo with `SPEC.md`, `PLAN.md`, a pytest suite and a pre-commit hook. Adapt the file names to your project.
- `ui-live-check` is written for Streamlit with a Playwright smoke script.
- Nothing here calls a paid API on its own: every live step asks first.
