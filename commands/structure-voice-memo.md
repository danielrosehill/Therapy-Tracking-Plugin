---
description: Turn a voice-memo transcript into a structured problem summary suitable for sharing with a therapist. The "I'm Not Okay" pattern. Reads the workspace path from `$CLAUDE_USER_DATA/therapy-tracking/config.json`.
---

# /therapy-tracking:structure-voice-memo

Take an unstructured transcript (or freeform text) and produce a tidy, readable summary the user can hand to their therapist. Organisation only — no interpretation.

## Procedure

1. **Resolve config**: Read `$PLUGIN_DATA_DIR/config.json`. If missing, tell the user to run `onboard` first and stop.

2. **Source the transcript**:
   - If `$ARGUMENTS` is a file path, read it.
   - Otherwise ask the user to paste the transcript or give a path.

3. **Edit for readability** (do not change meaning):
   - Remove filler words, false starts, immediate repetitions.
   - Insert paragraph breaks where the user pauses or shifts topic.
   - Fix obvious transcription errors (homophones, mis-segmented words) using context.
   - Keep the user's voice — do not rewrite into clinical or formal register.

4. **Extract discrete stressors / topics**: List each distinct issue the user named, in their own words. One short bullet each. Do not merge or interpret.

5. **Categorise** (only if the user asks): group bullets by topic the user themselves named. Never apply diagnostic categories.

6. **Write output**: Save to `<workspace_path>/therapy-sessions/YYMMDD-voice-memo-<slug>.md` with two sections:
   - **Cleaned transcript** — the edited version.
   - **Stressors / topics** — the bullet list.

7. **Report**: Print the file path. Suggest `/therapy-tracking:log-session pre` if the user wants to also write a forward-looking pre-session note.

## Rules

- **Never interpret feelings for the user.** Do not say "this sounds like anxiety" or "you seem to be describing X". Surface only what the user said.
- **Never add anything not in the source.** No inferred motivations, no synthesised conclusions.
- **Preserve voice.** The output should still sound like the user, just tidied.
