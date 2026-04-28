# Therapy Tracking Plugin

A Claude Code plugin for organising therapy reflections — pre/post-session notes, goal tracking, and turning voice-memo transcripts into structured problem summaries.

**Not therapy.** This plugin organises notes only; it does not diagnose, counsel, or replace a clinician.

Workspace data lives **outside** the plugin at `$CLAUDE_USER_DATA/therapy-tracking/` so the same install survives plugin updates and the data folder can be moved or version-controlled independently.

## Skills

- `onboard` — first-run interview. Captures modality, cadence, optional therapist reference, and workspace location. Provisions the data scaffold and writes `config.json`.

## Commands

- `/therapy-tracking:log-session` — pre or post-session note
- `/therapy-tracking:set-goal` — record / update a therapy goal
- `/therapy-tracking:review-progress` — summarise sessions and goal progress
- `/therapy-tracking:structure-voice-memo` — turn a transcript into a structured problem summary (the *I'm Not Okay* pattern)

## Storage convention

Follows the [`claude-rudder:plugin-data-storage`](https://github.com/danielrosehill/Claude-Rudder) convention:

- **Plugin code** lives at the install path (read-only, replaced on update).
- **User config** lives at `${CLAUDE_USER_DATA:-${XDG_DATA_HOME:-$HOME/.local/share}/claude-plugins}/therapy-tracking/config.json`.
- **Workspace** (session notes, goals, reviews) lives at the path you choose during onboarding — defaults to `$CLAUDE_USER_DATA/therapy-tracking/workspace/`.

## Privacy posture

- Default git mode is `local-only`. The `onboard` skill **never** suggests a public GitHub repo for this workspace.
- The therapist-reference field is optional and prompts for initials only.
- No clinical interpretation, diagnosis, or advice from any command.

## Quick start

1. Install the plugin.
2. Run the `onboard` skill — Claude will interview you and scaffold the workspace.
3. Use `/therapy-tracking:log-session`, `/therapy-tracking:set-goal`, `/therapy-tracking:review-progress`, or `/therapy-tracking:structure-voice-memo` from anywhere.

## License

MIT.
