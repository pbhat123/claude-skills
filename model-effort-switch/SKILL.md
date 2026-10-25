---
name: "model-effort-switch"
description: "Use when the user wants to change Claude Code's model or thinking/effort level, set new defaults, or asks which model and effort fit a task."
---

# Model and effort switch

## What can and cannot be changed from a skill
- The CURRENT session's model and effort can only be changed by the user typing `/model` and `/effort`. Always tell the user the exact command to type for an immediate change.
- A skill CAN change the DEFAULTS for future sessions by editing a settings file. Changes there apply from the next session.

## Step 1: recommend (cheat sheet)
Pick by task, then tell the user the command:

| Task | Model | Effort |
|---|---|---|
| Planning a big refactor or architecture (plan mode), tricky debugging, security/privacy reviews | opus | high or xhigh |
| Normal feature batches, test-first building in mock mode | default model | default (medium/high) |
| Diagnosing eval failures, reviewing reports, writing docs | default model | medium |
| Simple edits, status checks, renames, reading logs | sonnet or haiku | low |
| Long unattended paid runs (the run is a script; Claude only launches and reports) | default model | low or medium |

Rule of thumb: raise effort for decisions that are expensive to get wrong; lower it for mechanical work. Start from the model's default effort unless there is a reason.

Immediate change: tell the user to type `/model` (then choose) and `/effort` (then choose). `/effort` saves the level per model.

## Step 2: change defaults (only if the user asks)
1. Ask which scope: user-wide (`~/.claude/settings.json`) or this project only (`.claude/settings.json`, or `.claude/settings.local.json` if it should stay out of git).
2. Read the file first. Never overwrite it; edit only the keys below and keep every other key unchanged. Keep the JSON valid.
3. Keys:
   - `model`: alias (`opus`, `sonnet`, `haiku`) or a full model ID. Sets the model new sessions start with.
   - Effort per model: `modelSettings.<model-id>.effortLevel` with `low`, `medium`, `high` or `xhigh`. Prefer this over the top-level `effortLevel`, which in user settings only applies to older models.
   - Optional cap: `maxEffortLevel` (`low` to `max`).
   - If the full model ID for `modelSettings` is unknown, ask the user to check it in `/model`; never guess an ID.
4. Show the user the exact diff and get a yes before writing.
5. After writing, remind the user: the new defaults apply next session; use `/model` and `/effort` for now.

## One-session overrides (no file edits)
- Start Claude Code with `--model <alias-or-id>` and/or `--effort <level>`.
- Or set env vars for that run: `ANTHROPIC_MODEL`, `CLAUDE_CODE_EFFORT_LEVEL`.

## Never
- Never edit settings without showing the diff and getting a yes.
- Never remove or reorder unrelated settings (permissions, hooks, env).
- Never claim the current session changed because a file was edited.