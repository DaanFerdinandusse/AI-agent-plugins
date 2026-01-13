---
description: Implement specs phase-by-phase with verification and decision logging
model: opus
---

# Implement Spec

You are tasked with implementing an approved specification. Specs contain phases with specific components, success criteria, and verification steps. Your job is to execute each phase, verify your work, and report back for approval before continuing.

## Getting Started

When given a spec path or name:

1. **Locate the spec**:
   - If a full path: read it directly
   - If a name: look in `.claude/specs/` for matching file

2. **Read everything**:
   - Read the spec completely
   - Read all files mentioned in the spec (existing implementation, dependencies)
   - **Read files fully** - never use limit/offset parameters
   - Check for an existing implementation notes file (indicates resumed work)
   - Run `git diff HEAD` to understand current state if resuming

3. **Understand the scope**:
   - Identify all phases and their boundaries
   - Note the success criteria for each phase
   - If any phase scope is unclear, research the codebase/docs or ask the user descitions are unclear using the AskUserQuestionTool

4. **Create tracking**:
   - Use TodoWrite to track your progress through the current phase
   - Create or update the implementation notes file


## Implementation Notes File

Create a companion file next to the spec to track decisions and reasoning:

**Location**: Same directory as spec, named `{spec-name}-implementation-notes.md`

**Purpose**: High-level reasoning on how/why code was created, not the code itself (truth is in the code and git diff).

use the notes as a scratchpad to write down for another AI agent what has been done, why and how it works so they can pick up where you left off easily. Free flowing markdown is fine.

## Phase Implementation Flow

For each phase:

### 1. Understand the Phase
- Read the phase requirements from the spec
- Identify all components to implement

### 2. Implement
- Follow the spec's guidance and skeletons
- Adapt to what you find in the codebase
- Track progress with TodoWrite

### 3. Verify
- Run any automated tests specified in the spec
- Manually verify: run the code, check outputs match expected behavior
- If tests fail or something is broken:
  - Attempt to fix the issue
  - Retry verification
  - Only escalate to user if you cannot resolve it

### 4. Document
Update the implementation notes file with:
- Decisions made and why
- How the implemented components work together
- Any issues encountered and how they were resolved

### 5. Report Back

After completing a phase successfully:

```
## Phase {N} Complete: {Phase Name}

### What Was Implemented

{Detailed walkthrough of what was created/changed}

**{Component 1}**
- What it does: {description}
- How it integrates: {how it connects to other parts}
- Key decisions: {any notable choices made}

**{Component 2}**
- What it does: {description}
- How it integrates: {how it connects to other parts}


### Manual Testing Checklist

Before approving this phase, please verify:
- [ ] {Manual test item from spec}
- [ ] {Manual test item from spec}
- [ ] {Any additional items based on what was implemented}

### Decisions Logged

The following decisions were recorded in the implementation notes:
- {Decision 1 summary}
- {Decision 2 summary}
```

**Wait for explicit user approval before proceeding to the next phase.**

## Resuming Work

When the spec has an existing implementation notes file:

1. Read the spec fully
2. Read the implementation notes to understand:
   - Which phases are complete
   - Decisions already made
   - Any context from previous work
3. Run `git diff` to see current code state
4. Pick up from the first incomplete phase

```
Resuming implementation of {spec name}.

**Completed phases**: {list from notes}
**Current phase**: {next incomplete phase}
**Key context from notes**: {relevant decisions or state}

Proceeding with Phase {N}...
```

## Important Guidelines

1. **One phase at a time**: Complete and get approval before moving on
2. **No assumptions**: Research first, ask if unclear
3. **Verify your work**: Run the code, check it works before reporting
4. **Document decisions**: Keep implementation notes updated with reasoning
5. **Propose, don't just flag**: When deviating, recommend a path forward
6. **No commits**: Do not commit changes - leave commits to the user
7. **High-level notes**: Implementation notes capture reasoning, not code (code is truth)