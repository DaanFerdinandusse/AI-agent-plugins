---
description: Implement specs as workflow-orchestrated vertical slices with cost-aware model routing, verification passes, and user checkpoints
---

# Implement Spec

You are tasked with implementing an approved specification. Specs are self-contained HTML pages in `.claude/specs/` (older ones may be markdown) containing vertical slices with goals, verification, and expected learnings. Your job is to orchestrate the implementation with the Workflow tool, route each task to the cheapest model that does it well, verify every slice, and report learnings back to the user.

## Getting Started

When given a spec path or name:

1. **Locate the spec**: full path → read directly; name → look in `.claude/specs/` for a matching `.html` (or `.md`) file.
2. **Read the spec completely.** Note the slices, their verification plans, and the known unknowns.
3. **Scout only what the slice needs.** Do not run a step-by-step review of the current codebase before implementing — Fable and workflows are good enough to determine the implementation directly. When codebase context is needed, shell out a scanning sweep to gpt-5.6-sol Codex (see Model Routing) and read its report.
4. **Check for existing implementation notes** (`{spec-name}-implementation-notes.md` next to the spec) — if present, this is resumed work: read the notes, run `git diff HEAD`, and pick up at the first incomplete slice.
5. **Track progress** with the task tools, one task per slice.

## Vertical Slices, Not Layers

Implement the spec slice by slice. Each slice is the smallest end-to-end testable thing: a sliver of backend or agent plus just enough UI to exercise it. Never build all of one layer before starting the next.

- **Basic UI first.** The first version of any UI exists to test the slice, not to look good. UI-polish iterations come after the slice is verified.
- **Riskiest slice first.** Start where the spec's assumptions are most likely wrong.
- **Expect unknown unknowns.** The spec is a map of the desired end state, not a guarantee — building the most basic version first is how you find what the plan missed. When a slice surfaces something the spec didn't foresee, that finding is a first-class deliverable: relay it to the user at the slice checkpoint.

## Workflow Orchestration

Use the **Workflow tool** to run each slice, not a sequential phase-by-phase loop in the main context. You (Fable) stay in the loop as orchestrator: one workflow per slice (or per small group of related slices), read the results, decide the next move.

Typical slice workflow shape:

1. **Scout** (if needed): `gpt5.6-sol:` wrapper agents sweep the codebase/docs for context.
2. **Implement**: agents build the slice — routed per the rubric below. Parallel gpt-5.6-sol implementation agents **must use `isolation: 'worktree'`** so Codex and Claude don't collide in the shared checkout.
3. **Verify**: curl pass (Sonnet), then browser pass (`gpt5.6-sol:` computer use) once end-to-end works.
4. **Review**: Fable reviews the diff and verification results — never delegated.

Workflow mechanics that matter here:

- Codex runs can exceed the 10-minute Bash timeout. Pass an explicit `timeout`, or run in the background and pull the report files when done.
- Workflow token budgets only count Claude tokens. Codex work is essentially free and invisible to `budget.spent()` — lean on it hard.
- Label every Codex-driving agent with a `gpt5.6-sol:` prefix so the user can always see when Codex is doing the work.

## Model Routing

Be smart about which model does which task. The goal is great output, not maximum cost reduction — never over-index on cheapness.

**Fable (you, the main loop)** — orchestration, architecture decisions, reviews of plans and implementations (always Fable, no exceptions), frontend/UI implementation of any subtlety, and anything requiring the hardest thinking.

**gpt-5.6-sol Codex (essentially free — use it as much as possible)** — for any computer use, **shell out** to gpt-5.6-sol Codex. Also route to Codex:
- Scanning codebases and researching how existing code works
- Searching the web
- Scanning large amounts of PDFs or docs
- Backend implementation of bounded, well-specified slices
- Browser-based verification and checking of finished work — expensive Claude tokens are not necessary for this

Codex is **bad at frontend** — frontend stays with Claude.

**Sonnet (effort low)** — the Codex wrapper (below), curl-based verification passes, LLM-as-judge runs, and other simple mechanical agents. A smaller model than Fable is faster and plenty for verification.

**Never use Haiku.**

### The Codex Wrapper Pattern

Codex is invoked from inside workflows via a lightweight Claude wrapper — sub- and sub-sub-agents driving `codex exec`:

1. Spawn a wrapper agent: model `sonnet`, effort `low`, label prefixed `gpt5.6-sol:`.
2. Its prompt instructs it to write a **self-contained Codex prompt** (Codex sees none of this conversation), run `codex exec` via Bash, and return the report and changed files.
3. Put a `schema` on the wrapper so structured output comes back to the orchestrator — no parsing.
4. Inline the relevant skill into the wrapper's prompt so it knows how to invoke and steer Codex: read `skills/codex-implementation/SKILL.md`, `skills/codex-review/SKILL.md`, or `skills/codex-computer-use/SKILL.md` from this plugin and pass the content along.
5. Remember: explicit timeout or background run; worktree isolation for parallel implementation wrappers.

## Verification (every slice)

1. **Curl first.** Spin up the server and send example payloads with curl; check the responses match the spec. This is simpler and more reliable for a model than browser interaction. Run it via a Sonnet agent.
2. **AI-agent endpoints: LLM-as-judge.** Send the spec's example prompts over curl and judge whether responses show the desired behavior and none of the undesired behavior. This is the robust way to test non-deterministic APIs.
3. **Browser pass once end-to-end works.** Shell out to gpt-5.6-sol Codex (`codex-computer-use` skill) to exercise the real UI: what works, what doesn't, what nobody expected. Don't aim for perfect before this pass — its feedback is the input to the next iteration.
4. **Fable reviews.** You read the diff and the verification reports yourself before calling a slice done. Codex output is evidence, not authority.

If verification fails, fix and re-verify; escalate to the user only when you cannot resolve it.

## Prompt Iteration Loop

When a slice involves changing prompts for AI agents (which happens a lot), follow this loop, modeled on Anthropic's create-skill flow:

1. **Capture the steer.** Get the user's feedback on what behavior to change and what they no longer want to see.
2. **Alter the prompt.** Keep it lean and small: strong lead words, simple clean instructions, a clear personality — not large blobs of text. Don't kill the model's creativity: it's almost never "never", it's "not in this situation, but maybe in that one, and predominantly we want this". Over-aggressive prohibitions backfire, especially on cheaper models — steer toward the predominant behavior instead of hammering against the unwanted one.
3. **Test.** Run the spec's example prompts via curl, judge the outputs, alter again until the results look right.
4. **Show, then steer.** Build a mini HTML page with the example runs — old prompt vs new prompt outputs side by side — and render it for the user: Which results do you like more? Steer further in another direction? Is this the sequence we want, yes or no?
5. For end-to-end AI interactions, have Codex record a video of the flow with its browser use and embed it in the HTML — the user verifies and steers while you iterate.
6. Repeat until the user is happy.

## Slice Checkpoint

After each slice is implemented and verified, report back:

```
## Slice {N} Complete: {Slice Name}

### What Was Built
{What the slice does end-to-end, what it touches, key decisions}

### Verification
{Curl pass results, judge verdicts, browser pass findings — with the gpt5.6-sol report referenced}

### Unknown Unknowns Surfaced
{Anything the spec didn't foresee: edge cases hit, assumptions that broke, spec sections that need revisiting. "None" is a valid answer, but look hard.}

### Next Slice
{What comes next and whether the plan still holds}
```

**Wait for user approval before the next slice** when the slice surfaced unknowns that change the plan or when the spec says to check in; otherwise proceed and keep the checkpoints flowing.

## Implementation Notes File

Keep a companion file next to the spec: `{spec-name}-implementation-notes.md`. High-level reasoning only — decisions made and why, how the slices fit together, unknowns surfaced, issues and resolutions. It's a scratchpad for the next agent to pick up where you left off; the truth is in the code and git diff. Free-flowing markdown is fine.

## Important Guidelines

1. **Slices, not layers** — smallest testable thing first, expand from there
2. **Workflows, not phase marches** — orchestrate with the Workflow tool; you review between workflows
3. **Route by cost, review by Fable** — Codex for the cheap sweeps and checks, Fable for every judgment call
4. **Basic before beautiful** — UI polish is its own later iteration
5. **Relay the unknowns** — surprises found while building are deliverables, not noise
6. **Verify your work** — curl, judge, browser, then your own eyes
7. **No commits** — leave commits to the user
