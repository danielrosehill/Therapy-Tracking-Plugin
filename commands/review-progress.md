---
description: Summarise recent therapy sessions and progress against goals. Reads the workspace path from `$CLAUDE_USER_DATA/therapy-tracking/config.json`.
---

# /therapy-tracking:review-progress

Produce a concise progress review across recent session notes and goals.

## Procedure

1. **Resolve config**: Read `$PLUGIN_DATA_DIR/config.json` (see `onboard`). If missing, tell the user to run `onboard` first and stop.

2. **Gather**:
   - The 10 most recent files in `<workspace_path>/therapy-sessions/`.
   - All files in `<workspace_path>/goal-tracking/`.
   - The last entries in `<workspace_path>/logs/`.

3. **Summarise** (organisational, not interpretive):
   - For each goal, classify status as `on-track` / `slipping` / `stalled` / `achieved` based **only** on what the user has written. If unclear, say `unclear` rather than guess.
   - Identify recurring topics in recent sessions (themes the user has named).
   - Flag anything mentioned multiple times but never followed up on.

4. **Write review**: Save to `<workspace_path>/outputs/review-YYMMDD.md` with sections:
   - Goals snapshot (table)
   - Recurring topics (the user's own framing, quoted)
   - Loose threads (mentioned-but-not-followed-up items)
   - Open questions for the user — always phrased as questions, never as advice

5. **Present**: Show the review inline. Offer `/therapy-tracking:set-goal` to update goals if anything has shifted.

## Rules

- Never invent themes or interpretations the user didn't write. Quote, don't paraphrase, when summarising.
- Never offer advice, diagnosis, or "what you should bring up next session". Only surface what's in the workspace.
