---
name: onboard
description: First-run onboarding for the therapy-tracking plugin. Interview the user about therapy modality, session cadence, optional therapist reference, and workspace location, then provision the data scaffold and write `$CLAUDE_USER_DATA/therapy-tracking/config.json`. Run this before any other skill or command in this plugin, or whenever the setup changes. Triggers on phrases like "set up therapy tracking", "onboard therapy plugin", "configure therapy tracking".
disable-model-invocation: true
allowed-tools: Bash(mkdir *), Bash(cp *), Bash(cat *), Bash(test *), Bash(date *), Read, Write, Edit
---

# therapy-tracking: onboard

Establish the persistent profile and data scaffold for the therapy-tracking plugin. The same install can serve different profiles by re-running this skill — only the values in `config.json` and the contents of the workspace are user-specific. No personal details should ever be hard-coded into other commands in this plugin.

## Workspace resolution

Resolve the plugin's data directory as `$CLAUDE_USER_DATA/therapy-tracking/` if `CLAUDE_USER_DATA` is set; otherwise `$XDG_DATA_HOME/claude-plugins/therapy-tracking/` if `XDG_DATA_HOME` is set; otherwise `~/.local/share/claude-plugins/therapy-tracking/`. Create the directory if it doesn't exist.

Shell form:

```bash
PLUGIN_DATA_DIR="${CLAUDE_USER_DATA:-${XDG_DATA_HOME:-$HOME/.local/share}/claude-plugins}/therapy-tracking"
mkdir -p "$PLUGIN_DATA_DIR"
```

The config file is `$PLUGIN_DATA_DIR/config.json`. The workspace lives at `$PLUGIN_DATA_DIR/workspace/` by default — the user can override the workspace path during onboarding.

## When to use

- User says "set up therapy tracking", "onboard therapy plugin", "configure therapy tracking".
- Any other therapy-tracking command finds no `config.json` at the resolved path — offer to run onboarding first.
- The user changes therapist, modality, or wants to move the workspace.

## Procedure

### 1. Load existing config

If `$PLUGIN_DATA_DIR/config.json` exists, show the current values back. Offer **Update** vs **Replace** vs **Cancel**. On **Replace**, back up the existing file with `.bak.<timestamp>` suffix before writing.

### 2. Interview

Ask each question; default sensibly when the user just hits enter. Keep the tone light — this is a private setup, not a clinical intake.

| Field | Prompt | Notes |
|---|---|---|
| `workspace_path` | "Where should the therapy workspace live?" | Default: `$PLUGIN_DATA_DIR/workspace`. Accept any absolute path. Expand `~`. |
| `modality` | "Therapy modality (CBT, EMDR, psychodynamic, IFS, somatic, mixed, prefer-not-to-say)?" | Optional. Free text accepted. |
| `cadence` | "Session cadence (weekly, biweekly, monthly, ad-hoc)?" | Default: `weekly`. |
| `therapist_ref` | "Anything you'd like to record about the therapist? (initials only is fine; leave blank to skip)" | Optional. **Initials or first-name only by default** — discourage full names. |
| `private_repo` | "Should the workspace be a git repo? (no / local-only / private-github)" | Default: `local-only`. **Never** suggest a public GitHub repo for therapy data. |
| `notes` | "Any free-text notes about how you want to use this workspace?" | Optional. |

### 3. Provision the scaffold

If `<workspace_path>` does not exist, copy the bundled template into it:

```bash
mkdir -p "$WORKSPACE_PATH"
cp -r "${CLAUDE_PLUGIN_ROOT}/template/therapy-tracking/." "$WORKSPACE_PATH/"
```

If `${CLAUDE_PLUGIN_ROOT}` is not exported in skill bash injection, fall back to `${CLAUDE_SKILL_DIR}/../../template/therapy-tracking`.

Do **not** overwrite an existing workspace. If the path already exists and is non-empty, ask the user whether to use it as-is or pick a different path.

### 4. Personalise the workspace `CLAUDE.md`

Open `<workspace_path>/CLAUDE.md` and:

- Fill the `Workspace Facts` section with the modality, cadence, therapist reference (if any), and notes.
- Read `~/.claude/CLAUDE.md` if it exists and embed timezone / locale facts so downstream commands can skip re-asking.

### 5. (Optional) git init

Based on `private_repo`:

- `no` — do nothing.
- `local-only` — `git init`, initial commit. No remote.
- `private-github` — `git init`, commit, then `gh repo create <basename> --private --source=. --push`. Confirm with the user before running `gh`.

### 6. Write `config.json`

Pretty-printed, 2-space indent:

```json
{
  "schema_version": 1,
  "workspace_path": "/home/.../workspace",
  "modality": "CBT",
  "cadence": "weekly",
  "therapist_ref": null,
  "private_repo": "local-only",
  "notes": null,
  "updated_at": "<ISO-8601 timestamp>"
}
```

### 7. Summarise

Print every field and its final value. Show the path to `config.json` and the workspace. List the sibling commands the user can now invoke:

- `/therapy-tracking:log-session` — pre/post-session notes
- `/therapy-tracking:set-goal` — record or update a therapy goal
- `/therapy-tracking:review-progress` — summarise progress
- `/therapy-tracking:structure-voice-memo` — turn a transcript into a structured problem summary

## Hard rules

- **Privacy-first.** Never suggest a public GitHub repo. Default to local-only.
- **No clinical advice, ever.** This plugin organises notes; it does not diagnose, counsel, or replace a clinician. Reinforce this in the summary.
- **Discourage full names.** When asking for therapist reference, prompt for initials only.
- **Never store anything sensitive in `config.json` beyond what the user explicitly typed.** No transcript content, no session notes — those live in the workspace files.
- **Never hard-code paths or identifiers into other commands.** Every operational command must read `config.json`.
