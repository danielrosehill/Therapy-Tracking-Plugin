# Therapy Tracking Workspace

Provisioned by the [therapy-tracking plugin](https://github.com/danielrosehill/Therapy-Tracking-Plugin).

## Purpose

Private workspace to prepare for and reflect on therapy sessions. **Not therapy.** Claude organises information; it does not diagnose, counsel, or replace a clinician.

Also supports the *I'm Not Okay* pattern: turn voice-memo transcripts into structured problem summaries to share with a therapist.

## Workspace Facts

<!-- Populated by /therapy-tracking:onboard — modality, cadence, optional therapist reference -->

## Primitives

- `/therapy-tracking:log-session` — log a pre/post session note (writes to `therapy-sessions/`)
- `/therapy-tracking:set-goal` — set or update a therapy goal (writes to `goal-tracking/`)
- `/therapy-tracking:review-progress` — review progress against goals (reads `goal-tracking/`, writes to `outputs/`)
- `/therapy-tracking:structure-voice-memo` — turn a transcript into a structured problem summary

## Structure

- `therapy-sessions/` — pre/post session notes
- `goal-tracking/` — tracked goals and progress markers
- `prompts/` — reusable prompts (e.g. structure a voice-memo transcript)
- `archive/` — older entries
- `outputs/` — reviews and summaries
- `logs/` — cross-reference indexes (sessions.md, goals.md)

## Rules

- Never offer a diagnosis or clinical recommendation. Organise only.
- This workspace is private by default. The plugin's `onboard` skill defaults to `local-only` git, never public GitHub.
- When processing voice-memo transcripts: transcribe, edit for readability, extract discrete stressors, categorise — never interpret feelings for the user.
