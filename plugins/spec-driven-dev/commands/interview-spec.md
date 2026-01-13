---
description: Create comprehensive spec documents through deep iterative interviews with proactive research
model: opus
---

# Interview Spec

You are tasked with creating comprehensive specification documents through a deep, iterative interview process. Your role is to ask non-obvious questions, conduct proactive research, and produce a spec detailed enough for an AI implementation agent with zero prior context to execute.

Interview the user in detail using the AskUserQuestionTool about literally anything: technical implementation, UI & UX, concerns, tradeoffs, etc. but make sure the questions are not obvious. Be very in-depth and continue interviewing the user continually until you have complete understanding, then write the spec to the file.

## Initial Response

When this command is invoked:

1. **Check if an initial description was provided**:
   - If the user provided a project description, read it carefully
   - Identify what's clear and what needs exploration
   - *(optional)* spawn research agents for context on:
   - Then begin the interview process

2. **If no description provided**, respond with:
```
I'll help you create a comprehensive specification document. To get started, please describe:

1. What are you building? (the problem and who it's for)
2. What's the core functionality?
```

Then wait for the user's input.

## Process Overview

This is an **iterative conversation** that flows through these stages:

0. (Optional) **Initial Context & Research** - Understand the project, proactively research
1. **Deep Interview** - Ask non-obvious questions across multiple dimensions
2. **Spec Writing** - Write the comprehensive specification document

## Stage 0: Initial Context & Research

After receiving the initial description:

### Research (When Needed)

Assess whether research would be valuable before starting the interview. Not every project needs upfront research:

- **Greenfield projects** with no existing codebase → Skip codebase research
- **Completly independent new features** without existing codebase dependencies → Skip package research

When research is wanted **ALWAYS** spawn subagents to gather context (in parallel if multiple areas need research). The agents you are available to use are:

- `spec-driven-dev:spec-doc-researcher` - for external packages/APIs mentioned
- `spec-driven-dev:spec-codebase-explorer` - for existing codebase being modified

### Beginning the Interview

If research was done, synthesize findings and ask informed questions. If not, proceed directly to questioning.

## Stage 1: Deep Interview Process

### Question Philosophy

Ask questions that are **non-obvious**. You can ask questions on anything from technical implementation, UI & UX, concerns, tradeoffs, etc. but make sure the questions are not obvious. 

### Question Mechanics

- Ask **1-5 questions at a time** depending on complexity
- You can **reflect and summarize** to show your thinking before asking more questions
- You can **provide context** in text before using AskUserQuestion
- Use AskUserQuestion for structured choices, but speak conversationally too


## Stage 2: Writing the Specification

The focus on the spec is to be a comprehensive description of the project that is executable by an AI agent with zero context based on everything you have learned from the interview with the user. If the interview is mostly around business requirements, the spec should reflect that.

### File Location and Naming

Write to: `./.claude/specs/{project-name}-{YYYY-MM-DD}.md`

- Use a sensible kebab-case project name
- Date as postfix (today's date)
- Example: `./.claude/specs/plugin-marketplace-2025-01-04.md`

### Specification Template

The spec must be **self-contained** - an AI agent with no context from this conversation should be able to implement it.

````markdown
# {Project Name} Specification

> {One-line description of what this builds}

## Executive Summary

{2-4 paragraphs covering:}
- What we're building and for whom
- The core problem being solved
- High-level approach
- Key constraints or requirements

### Core Features
1. **{Feature 1}** - {Brief description}
2. **{Feature 2}** - {Brief description}
3. **{Feature 3}** - {Brief description}

---

## Key Design Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| {Decision area} | {What we chose} | {Why, and what alternatives were considered} |
| {Decision area} | {What we chose} | {Why, and what alternatives were considered} |

---

## All Design Decisions

{Every decision made during the interview, synthesized into clean format}

### {Category 1}

**Decision**: {What was decided}
**Rationale**: {Why this choice, what alternatives were considered}

**Decision**: {What was decided}
**Rationale**: {Why this choice, what alternatives were considered}

### {Category 2}

{Continue for all decisions...}

---

## Architecture

### System Overview

{High-level description of how the system fits together}

### UI Components

{Only include for user-facing features}

#### {Component/View Name}

**Layout**:
```
┌─────────────────────────────────────────────────────────┐
│  {Header area}                                          │
├─────────────────────────────────────────────────────────┤
│  {Main content area}                                    │
│                                                         │
│  ┌─────────────┐  ┌─────────────┐                      │
│  │  {Element}  │  │  {Element}  │                      │
│  └─────────────┘  └─────────────┘                      │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

**Interactions**:
- {User action} → {System response}
- {User action} → {System response}

**States** (scale detail to complexity):
- Default: {Description}
- Loading: {Description}
- Error: {Description}
- Empty: {Description}


### Data Flow

```
{ASCII diagram showing how data moves through the system}
```

### Component Architecture

```
{File tree showing the structure}
project/
├── {directory}/
│   ├── {file}                    # {What this file does}
│   └── {file}                    # {What this file does}
└── {directory}/
    └── {file}                    # {What this file does}
```

---

## Existing Implementation (optional)

{Only include if modifying existing code}

### Current State

{Description of what exists now}

### Key Code Excerpts

**{Component/File being modified}** (`{path}`)
```{language}
{Relevant code snippet that will be modified or extended}
```

**Modification approach**: {How we're changing/extending this}

---

## External Dependencies (optional)

### {Package/API Name} (v{version}) 

{Only include for external packages/APIs being used}

**Purpose**: {Why we're using this}

**Key APIs**:
```{language}
// {Function/class we'll use}
{API signature from documentation}
```

**Important Patterns**:
- {Pattern or constraint from docs}
- {Version-specific consideration}

### External Data Structures 

{If consuming external APIs, show the data structures}

**{API/Source Name}** response structure:
```json
{
  "field": "type - description",
  "nested": {
    "field": "type - description"
  }
}
```

---

## Database Schema (optional)

{Only include if creating/modifying database}

```sql
-- {Table description}
CREATE TABLE {table_name} (
  {column} {type} {constraints},
  {column} {type} {constraints}
);

-- Indexes
CREATE INDEX {index_name} ON {table}({columns});
```

---

## Storage Structure (optional)

{Only include if using file/object storage}

```
{bucket/directory}/
├── {path}/
│   └── {file pattern}           # {What these files contain}
└── {path}/
    └── {file pattern}           # {What these files contain}
```

---

## API Design (optional)

{TypeScript/language skeletons - minimal, not full implementations}

### {Endpoint/Function Group}

```typescript
// {Description of what this does}
async function {functionName}(
  {param}: {Type},
  {param}: {Type}
): Promise<{ReturnType}> {
  // TODO: Implement
  // 1. {Step 1}
  // 2. {Step 2}
  // 3. {Step 3}
}
```

**Parameters**:
- `{param}` ({Type}): {Description}

**Returns**: {Description of return value}

---


## Implementation Phases

### Phase 1: {Phase Name}

**Goal**: {What this phase accomplishes}

**Components**:
1. {Component/task}
2. {Component/task}
3. {Component/task}

**Verification**:
- [ ] {Testable criteria}
- [ ] {Testable criteria}

---

### Phase 2: {Phase Name}

{Same structure...}

---

## Testing Strategy

### {Component/Area}

**Automated**:
- [ ] {What to test and how to verify}
- [ ] {What to test and how to verify}

**Manual**:
- [ ] {What to verify manually}

---

## Security Considerations

- {Security consideration and how it's addressed}
- {Security consideration and how it's addressed}

---

## Future Considerations

{Only items explicitly deferred by the user during the interview}

- {Deferred item} - {Why it's deferred}
- {Deferred item} - {Why it's deferred}

---

## Unresolved Questions

{Any questions that remain open - ideally empty}

- {Question} - {Context on why it's unresolved}
````

### Writing Guidelines

1. **Self-Contained**: Include all context an implementer needs
2. **Decisions Have Rationale**: Always explain why, not just what
3. **Pin Versions**: Include specific version numbers from research
4. **Code Excerpts**: Show key existing code being modified
5. **External APIs**: Include actual signatures from documentation
6. **UI States**: Scale detail to component complexity
7. **Testing**: Include verification criteria for each component
8. **No Silent Assumptions**: If it's not obvious, it was asked about

## Important Guidelines

1. **Research When Valuable**: Research before asking if external packages, APIs, or existing code are involved - but skip it for greenfield or simple projects
2. **Non-Obvious Questions**: Focus on judgment calls, tradeoffs, edge cases
3. **No Assumptions**: If it's non-obvious, ask. If it's obvious, don't mention it.
4. **Complete Spec**: The output should be executable by an AI with zero context
5. **Synthesized Decisions**: Clean "Decision + Rationale" format, not raw Q&A
