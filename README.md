# Copilot Feature AI Framework

This repository is an early framework for organizing AI-assisted feature development in brownfield codebases.

The scope is intentionally narrow:

- Feature development only.
- Brownfield projects only.
- VS Code Copilot first.
- Minimal artifacts and low ceremony.

The framework is designed so it can later support other agents, but version 1 is centered on Copilot primitives such as custom instructions, prompts, agents, and skills.

## Goal

The goal is to make Copilot sessions predictable and reusable across a team by giving the agent a small set of durable artifacts to read and update instead of relying on chat history.

Each flow is expected to run in a fresh session. The files are the source of continuity.

## Core Ideas

- Keep the artifact set small.
- Separate planning artifacts from execution state.
- Make it easy for the agent to discover the next unit of work.
- Support both small features and larger features without introducing unnecessary process.

## Copilot Primitives

This framework currently uses the following Copilot-oriented primitives:

- `github/copilot-instructions.md`
- `github/agents/*.agent.md`
- `github/prompts/*.prompt.md`
- `github/skills/<skill-name>/SKILL.md`

This repository currently keeps them under `github/` as framework assets. When you apply the framework inside a real project, place them in the Copilot-recognized locations that project will use.

## Artifact Model

### Feature Level

- `brief.md`: raw feature intent, business ask, and initial constraints.
- `plan.md`: clarified requirements and implementation strategy for the feature.
- `phases.status.json`: used for small features that can go directly from plan to phases.
- `epics.status.json`: used for larger features that first need to be split into epics.

### Epic Level

- `brief.md`: epic-specific restatement if needed.
- `plan.md`: clarified requirements and implementation strategy for the epic.
- `phases.status.json`: ordered phase state for that epic.

### Phase Level

- `phase.md`: the human-readable phase definition and later the task breakdown for implementation.
- `report.md`: written after the phase is completed.

## Status Files

Status files exist to hold machine-friendly state, not the implementation narrative.

- `epics.status.json` tracks epic progress.
- `phases.status.json` tracks phase progress and task progress.
- The markdown files describe what should be built and why.
- The status files describe what is currently done, in progress, or next.

### Status Conventions

- Status values are `not-started`, `in-progress`, and `completed`.
- Each epic or phase entry includes a stable `id`, a human-readable `name`, and a relative `path`.
- Phase entries also include task state entries.
- The agent should use these files to locate the next actionable item quickly.

JSON schema validation is configured in `.vscode/settings.json`.

## Supported Shapes

### Small Feature

A small feature can move directly from feature planning into phase execution.

Example:

- `features/<feature>/brief.md`
- `features/<feature>/plan.md`
- `features/<feature>/phases.status.json`
- `features/<feature>/phases/<phase>/phase.md`
- `features/<feature>/phases/<phase>/report.md`

### Big Feature

A larger feature is first split into epics, and each epic manages its own phases.

Example:

- `features/<feature>/brief.md`
- `features/<feature>/plan.md`
- `features/<feature>/epics.status.json`
- `features/<feature>/epics/<epic>/brief.md`
- `features/<feature>/epics/<epic>/plan.md`
- `features/<feature>/epics/<epic>/phases.status.json`
- `features/<feature>/epics/<epic>/phases/<phase>/phase.md`
- `features/<feature>/epics/<epic>/phases/<phase>/report.md`

## Flows

### 1. Init Flow

Preparation and codebase analysis that runs once per target project.

This part is intentionally still open because the framework is expected to incorporate modern MCP servers and external skills over time.

### 2. Requirements Generation Flow

Input:

- `brief.md`

Output:

- `plan.md`

This flow expands a brief into a more rigorous feature or epic plan, including edge cases, implementation details, and decision branches.

### 3. Phase Generation Flow

Input:

- `plan.md`

Output:

- `phases.status.json` or `epics.status.json`
- phase folders and `phase.md` files

This flow decides whether the work should proceed directly through phases or first through epics.

### 4. Phase Task Creation Flow

Input:

- the relevant status file
- `phase.md`

Output:

- updated `phase.md` with an actionable task breakdown

The agent first determines what phase it should work on, then gathers codebase and user context, and finally breaks the phase into implementation tasks.

### 5. Phase Implementation Flow

Input:

- tasks in `phase.md`
- current state in `phases.status.json`

Output:

- code changes
- updated task and phase state
- `report.md` when the phase is finished

The agent implements tasks in order, updating the relevant status file after each completed task and marking the phase complete when the phase work is done.

## Current State

This repository is still at an early design stage. The current examples exist to validate the structure, naming, and workflow boundaries before the automation layer is built out.
