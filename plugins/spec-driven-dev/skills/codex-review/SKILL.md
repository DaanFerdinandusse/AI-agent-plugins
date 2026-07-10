---
name: codex-review
description: Ask Codex CLI (gpt-5.5) for an independent code review of uncommitted changes, a branch diff, a commit, or a specific implementation. This is how gpt-5.5 is invoked for review work. Use when the user asks Claude to have Codex or gpt-5.5 review work, when the model-selection rubric calls for a gpt-5.5 review perspective, or when Codex should audit a diff, find bugs or regressions, or compare Claude's implementation against requirements. For a review by Claude itself, use the normal review process instead.
---

# Codex Review

Use Codex as an independent reviewer when the user wants a second-pass review or when a change is broad enough that another agent's perspective is useful.

Prefer Claude's normal review process for small local checks. Do not delegate review just to avoid reading the code yourself. Treat Codex's output as evidence, not authority.

## Workflow

1. Identify the review target: uncommitted changes, base branch, commit SHA, PR checkout, or specific files.
2. Create a temporary artifact directory for the Codex report.
3. Run `codex review` with a focused review prompt.
4. Read Codex's report and verify important claims against the code before presenting them.

Use one of these command shapes:

```bash
ARTIFACT_DIR="$(mktemp -d "${TMPDIR:-/tmp}/codex-review.XXXXXX")"
REPORT="$ARTIFACT_DIR/report.md"
PROMPT="$ARTIFACT_DIR/prompt.md"

# Review staged, unstaged, and untracked changes.
codex -C "$PWD" review --uncommitted - < "$PROMPT" > "$REPORT"

# Review current branch against a base branch.
codex -C "$PWD" review --base main - < "$PROMPT" > "$REPORT"

# Review a single commit.
codex -C "$PWD" review --commit <sha> - < "$PROMPT" > "$REPORT"
```

## Review Prompt

Ask Codex to use a code-review stance:

```text
Review these changes for bugs, regressions, missing tests, security issues, and requirement mismatches.

Prioritize findings over summary. For each finding include:
- severity
- file and line reference
- concrete failure mode
- suggested fix direction

Do not edit files. If there are no substantive findings, say so and name any residual test gaps.
```

Tailor the prompt with relevant requirements, tests, or files Claude is unsure about.

## Reporting Back

Before relaying a Codex finding, inspect the cited code or diff enough to decide whether the finding is real. In the user-facing response, separate confirmed issues from Codex suggestions you did not verify.

If Codex finds nothing, say that clearly and mention what review target it inspected.

If `codex` is not installed or the command fails, report the error and offer to review the changes directly instead.
