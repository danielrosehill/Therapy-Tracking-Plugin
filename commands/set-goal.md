---
description: Create or update a therapy goal in the therapy-tracking workspace. Reads the workspace path from `$CLAUDE_USER_DATA/therapy-tracking/config.json`.
---

# /therapy-tracking:set-goal

Record a new therapy goal or update an existing one in `goal-tracking/`.

## Procedure

1. **Resolve config**: Read `$PLUGIN_DATA_DIR/config.json` (see `onboard`). If missing, tell the user to run `onboard` first and stop.

2. **Collect**: From `$ARGUMENTS` or by asking:
   - Goal title (kebab-case slug)
   - Description / what "progress" would look like
   - Target horizon (next session / month / open-ended)
   - Review cadence (weekly / monthly / per-session / ad-hoc)
   - Linked sessions (optional — filenames in `therapy-sessions/`)

3. **Write or update**: Target file is `<workspace_path>/goal-tracking/<slug>.md`. If it exists, propose a diff and confirm before overwriting. Otherwise create it with structured front-matter (`title`, `created`, `horizon`, `cadence`) plus narrative.

4. **Cross-link**: Append a one-liner in `<workspace_path>/logs/goals.md` (create if missing).

5. **Report**: Print the file path. Suggest `/therapy-tracking:review-progress` to see how it sits against existing goals.

## Rules

- Goals here are the user's own — never suggest goals for them. Only structure what they bring.
- Never frame a goal in clinical or diagnostic language unless the user used that language first.
