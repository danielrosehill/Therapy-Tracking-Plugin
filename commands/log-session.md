---
description: Record a pre- or post-therapy-session note in the therapy-tracking workspace. Reads the workspace path from `$CLAUDE_USER_DATA/therapy-tracking/config.json`.
---

# /therapy-tracking:log-session

Append a dated session note (pre-session intentions or post-session reflections) into the workspace's `therapy-sessions/` folder.

## Procedure

1. **Resolve config**:

   ```bash
   PLUGIN_DATA_DIR="${CLAUDE_USER_DATA:-${XDG_DATA_HOME:-$HOME/.local/share}/claude-plugins}/therapy-tracking"
   ```

   Read `$PLUGIN_DATA_DIR/config.json`. If it doesn't exist, tell the user to run the `onboard` skill first and stop.

2. **Determine kind**: Parse `$ARGUMENTS` for `pre` / `post` (default: ask). A pre-session note is forward-looking ("what I want to bring up"); a post-session note reflects on what happened.

3. **Collect content**: If the rest of `$ARGUMENTS` is empty, ask for the body. Otherwise treat it as free text.

4. **Write file**: At `<workspace_path>/therapy-sessions/YYMMDD-<pre|post>-<short-slug>.md` with front-matter (`date`, `kind`, optional `tags`) and the body.

5. **Cross-reference**: Append a one-liner (date + filename + first line) to `<workspace_path>/logs/sessions.md` (create if missing).

6. **Report**: Print the file path. Suggest `/therapy-tracking:review-progress` if it's been a while since the last review.

## Rules

- Never offer interpretation, diagnosis, or clinical recommendation. Organise text only.
- If the user shares something that sounds acutely distressing, gently note that this tool is for organisation and a clinician or crisis line is the right place — but do not lecture.
