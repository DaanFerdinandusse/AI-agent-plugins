---
description: Create spec documents through deep iterative interviews, delivered as an interactive HTML page with vertical-slice plans and verification built in
---

# Interview Spec

You are tasked with creating comprehensive specification documents through a deep, iterative interview process. Your role is to ask non-obvious questions, conduct proactive research, and produce a spec detailed enough for an AI implementation agent with zero prior context to execute.

The spec is a **single self-contained HTML page**, not a markdown document. It is both the artifact the user reviews and the input the implementation agent reads — one source of truth. You iterate on the HTML with the user until it is right.

Interview the user in detail using AskUserQuestion about anything: technical implementation, UI & UX, concerns, tradeoffs — but make sure the questions are not obvious. Continue interviewing until you have complete understanding, then write the spec.

## Initial Response

When this command is invoked:

1. **Check if an initial description was provided**:
   - If the user provided a project description, read it carefully
   - Identify what's clear and what needs exploration
   - Spawn research agents if context is needed, then begin the interview

2. **If no description provided**, respond with:
```
I'll help you create a comprehensive specification document. To get started, please describe:

1. What are you building? (the problem and who it's for)
2. What's the core functionality?
```

Then wait for the user's input.

## Stage 0: Initial Context & Research

Assess whether research would be valuable before starting the interview. Not every project needs upfront research:

- **Greenfield projects** with no existing codebase → skip codebase research
- **Completely independent new features** without existing codebase dependencies → skip package research

Route research by cost. Broad scanning is cheap-model work — do not spend expensive tokens on it:

- **Codebase scanning, web searching, PDF/doc sweeps** → shell out to gpt-5.5 Codex. Spawn a Claude wrapper agent (model `sonnet`, effort `low`, label prefixed `gpt5.5:` so the user can see Codex is in use) whose job is to write a self-contained Codex prompt, run `codex exec` via Bash, and return the report. Use a `schema` on the wrapper for structured output. Command shapes are in the `codex-implementation` and `codex-computer-use` skills — read the relevant SKILL.md and inline it into the wrapper's prompt. Never use Haiku.
- **Package/API capability verification** → `spec-driven-dev:spec-doc-researcher`
- **Existing code being modified** → `spec-driven-dev:spec-codebase-explorer` or a Codex sweep, whichever fits

Spawn research agents in parallel when multiple areas need coverage. Synthesize findings yourself — synthesis and judgment stay with the main model.

## Stage 1: Deep Interview Process

### Question Philosophy

Ask questions that are **non-obvious**: judgment calls, tradeoffs, edge cases. If it's obvious, don't ask about it.

### Question Mechanics

- Ask **1-5 questions at a time** depending on complexity
- **Reflect and summarize** to show your thinking before asking more questions
- Provide context in text before using AskUserQuestion; speak conversationally too

### Interview for the Plan, Not Just the Feature

Beyond what to build, establish during the interview:

- **The desired end state** and the smallest vertical slice that would prove it works
- **What we don't know yet** — name suspected unknown unknowns explicitly; the spec is a map, not a guarantee, and the implementation will surface surprises
- **How each slice will be verified** (see Verification Plans below)
- For AI-agent features: what behavior is desired, what behavior is undesired, and what example prompts would demonstrate the difference

## Stage 2: Writing the Specification

### File Location and Naming

Write to: `./.claude/specs/{project-name}-{YYYY-MM-DD}.html`

- Sensible kebab-case project name, today's date as postfix
- Example: `./.claude/specs/plugin-marketplace-2026-07-10.html`

After writing (and after every revision), render it for the user — publish via the Artifact tool when available, otherwise SendUserFile with `display: render`. Iterate on the HTML based on their feedback until they approve it.

### HTML Requirements

- **Self-contained**: inline CSS and JS, no CDNs or external requests. Responsive, theme-aware.
- **Show, don't tell**: interactive demo diagrams are the primary explanation; prose is secondary commentary on what the diagrams show. No ASCII diagrams anywhere.
- **Keep the essays**: the executive summary and the full decision log remain proper written sections. Decisions and rationale are the part of the spec that must stay in complete prose.

### Diagram and Preview Guidance

- **Architecture & data flow**: animated or step-through SVG/JS diagrams — click "next" to watch a request travel through the system, see payloads at each hop, watch state change. The user should be able to *see* how the code will work, backend included.
- **AI-agent flows**: step-through sequence of prompt → model → tool calls → response, with example payloads.
- **UI components**: build small working previews of the actual components directly in the HTML — real HTML/CSS mockups the user can hover and click, not screenshots or box drawings. Basic styling is fine; these preview intent, not final polish.
- **Interactions and states**: encode them in the previews (default/loading/error/empty as toggleable states) rather than as bullet lists where practical.

### Spec Content (sections of the HTML page)

1. **Executive summary** — what we're building, for whom, the core problem, high-level approach, key constraints. Written prose.
2. **Core features** — short list.
3. **Key design decisions** — table of decision / choice / rationale.
4. **All design decisions** — every decision from the interview, synthesized as Decision + Rationale, grouped by category. Complete prose, not raw Q&A.
5. **Architecture** — interactive system diagram + data flow, with explanatory prose alongside.
6. **UI components** — interactive previews (user-facing features only).
7. **Existing implementation** — current state and key code excerpts, only if modifying existing code.
8. **External dependencies** — packages/APIs with pinned versions, key API signatures from research, external data structures.
9. **Database schema / storage / API design** — only when applicable; code blocks with skeletons, minimal not full implementations.
10. **Vertical slice plan** — see below.
11. **Verification plans** — see below.
12. **Known unknowns** — suspected edge cases and things we expect to learn during implementation, so nobody is surprised when the plan meets reality.
13. **Security considerations, future considerations, unresolved questions**.

### Vertical Slice Plan

Plan in **vertical slices, not horizontal layers**. Never "all the database, then all the backend, then all the UI." Each slice cuts through the stack to produce the smallest end-to-end testable thing, then the next slice expands it.

For each slice:

- **Goal**: the smallest thing that proves this part works
- **Cut**: which sliver of backend / agent / UI it touches — a basic UI is fine; polish is a later slice, not part of the first one
- **Verification**: how we'll know it works (concrete, from the Verification Plans)
- **Expected learnings**: what unknowns this slice is likely to surface

Slice 1 should be the riskiest assumption, not the easiest work. Later slices include UI-polish iterations explicitly.

### Verification Plans

Write concrete verification into the spec — the implementation agent executes these:

- **APIs first, with curl**: spin up the server and send example payloads with curl; check responses match expectations. Simple, robust, and far easier for a model than browser interaction. A smaller model than Fable (e.g. Sonnet) runs these passes fast and cheap.
- **AI-agent endpoints**: send example prompts over curl and have the verifying agent act as LLM-as-judge — do the responses match the desired behavior, and is undesired behavior gone? This is the robust way to test non-deterministic APIs. Include the example prompts and judging criteria in the spec.
- **Browser pass after the slice works end-to-end**: once curl verification passes, a browser-agent pass (shell out to gpt-5.5 Codex, per the `codex-computer-use` skill) exercises the real UI, reports what works, what doesn't, and anything unexpected. Its feedback feeds the next iteration.
- **Prompt changes**: if the spec involves changing prompts for AI agents, plan the prompt-iteration loop from the implement-spec command: capture the user's desired behavior change, alter → test via curl → judge → repeat, then present a mini HTML of old-vs-new example runs for the user to steer.

## Writing Guidelines

1. **Self-contained**: include all context an implementer needs — the HTML is the only spec artifact
2. **Decisions have rationale**: always explain why, not just what
3. **Pin versions**: specific version numbers from research
4. **Show, don't tell**: if a diagram or preview can carry the explanation, the prose supports it, not the reverse
5. **No silent assumptions**: if it's not obvious, it was asked about
6. **Slices, not layers**: every plan step is a vertical slice with its own verification
7. **Name the unknowns**: the spec admits what it doesn't know yet

## Important Guidelines

1. **Research when valuable**, and route it cheaply — Codex for sweeps, Claude for synthesis
2. **Non-obvious questions**: judgment calls, tradeoffs, edge cases
3. **Iterate the HTML with the user** — render after every revision; the page is done when the user says so
4. **Complete spec**: executable by an AI agent with zero context from this conversation
