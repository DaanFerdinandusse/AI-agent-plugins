---
name: codex-computer-use
description: Ask Codex CLI (gpt-5.6-sol) to run local app verification that needs computer use, browser automation, simulators, screenshots, app launching, or independent runtime inspection. This is how gpt-5.6-sol is invoked for computer-use work. Use when the user asks Claude to have Codex or gpt-5.6-sol test a flow, verify UI behavior, inspect a running app, capture screenshots, or report confirmation and feedback about implemented behavior.
---

# Codex Computer Use

Use Codex as a separate local verification agent when the task needs real UI interaction, screenshots, simulator/browser/device state, or an independent runtime check outside Claude's current context.

Do not use this for ordinary code reading, typechecking, linting, or tests Claude can run directly. Launching apps, simulators, or browsers to verify the requested work is fine without asking; ask first only if the run could disrupt the user's environment beyond that, such as closing their apps, changing system settings, or acting on real accounts or data.

## Workflow

1. Create a temporary artifact directory.
2. Give Codex a self-contained prompt with the repo path, exact flow, constraints, artifact directory, and report format.
3. Run `codex exec` non-interactively.
4. Read Codex's report, inspect or reference screenshot paths, and summarize the result for the user.

Use this command shape:

```bash
ARTIFACT_DIR="$(mktemp -d "${TMPDIR:-/tmp}/codex-computer-use.XXXXXX")"
REPORT="$ARTIFACT_DIR/report.md"
PROMPT="$ARTIFACT_DIR/prompt.md"

# Write a self-contained prompt to $PROMPT, then run:
codex exec \
  -C "$PWD" \
  --add-dir "$ARTIFACT_DIR" \
  -s danger-full-access \
  -o "$REPORT" \
  "$(cat "$PROMPT")"
```

Use `-s danger-full-access` for GUI automation, iOS simulators, desktop app launching, screenshots, or access outside the repo. For non-GUI checks that only need the repo and artifact directory, prefer `-s workspace-write`. Add `--skip-git-repo-check` when the working directory is not a Git repository.

## Prompt Requirements

Tell Codex:

- The exact behavior to verify.
- The platform and app type, such as iOS, web, Electron, CLI, or desktop.
- Known launch commands, test credentials, seed data, deep links, or fixtures.
- Whether source edits are allowed. Default to no edits.
- Where screenshots, logs, and the final report should be saved.
- To return pass, fail, or blocked, plus steps performed, observed behavior, screenshot paths, and actionable feedback.

Keep the prompt specific enough that Codex does not need the surrounding Claude conversation.
