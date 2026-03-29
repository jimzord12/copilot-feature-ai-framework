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

### Project Level

- `init-summary.md`: mandatory output of the init flow, created once per target codebase and reused by later sessions.

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

- `phase.md`: the stable phase definition, context, and task indexing document.
- `tasks/*.md`: the actionable task files used during implementation.
- `report.md`: written after the phase is completed.

## Templates

The framework currently uses the following document templates:

- `templates/init-summary.template.md`
- `templates/brief.template.md`
- `templates/plan.template.md`
- `templates/phase.template.md`
- `templates/task.template.md`
- `templates/report.template.md`

The init summary template is mandatory because the framework expects later sessions to rely on durable project context instead of prior chat history.

## Status Files

Status files exist to hold machine-friendly state, not the implementation narrative.

- `epics.status.json` tracks epic progress.
- `phases.status.json` tracks phase progress and task-file progress.
- The markdown files describe what should be built and why.
- The status files describe what is currently done, in progress, or next.

### Status Conventions

- Status values are `not-started`, `in-progress`, and `completed`.
- Each epic, phase, or task entry includes a stable `id`, a human-readable `name`, and a relative `path`.
- Phase and task entries also include a short `summary` for quick selection before opening their markdown files.
- The agent should use these files to locate the next actionable item quickly.

### Phases Status Shape

`phases.status.json` is the single machine-readable source of truth for phase and task execution state. It should point directly at task files and should not require a separate `tasks.status.json`.

```json
[
  {
    "id": "001-create-tables",
    "name": "Create Auth Tables",
    "path": "phases/001-create-tables",
    "summary": "Create the database structures required to support the auth integration epic.",
    "status": "in-progress",
    "tasks": [
      {
        "id": "001-create-user-table",
        "name": "Create user table",
        "path": "phases/001-create-tables/tasks/001-create-user-table.md",
        "summary": "Add the primary user table and its core columns needed by the auth flow.",
        "status": "completed"
      }
    ]
  }
]
```

The `path` values are relative to the containing feature root for small features and relative to the containing epic root for epic phases.

JSON schema validation is configured in `.vscode/settings.json`.

## Supported Shapes

### Small Feature

A small feature can move directly from feature planning into phase execution.

Example:

- `init-summary.md`
- `features/<feature>/brief.md`
- `features/<feature>/plan.md`
- `features/<feature>/phases.status.json`
- `features/<feature>/phases/<phase>/phase.md`
- `features/<feature>/phases/<phase>/tasks/<task>.md`
- `features/<feature>/phases/<phase>/report.md`

### Big Feature

A larger feature is first split into epics, and each epic manages its own phases.

Example:

- `init-summary.md`
- `features/<feature>/brief.md`
- `features/<feature>/plan.md`
- `features/<feature>/epics.status.json`
- `features/<feature>/epics/<epic>/brief.md`
- `features/<feature>/epics/<epic>/plan.md`
- `features/<feature>/epics/<epic>/phases.status.json`
- `features/<feature>/epics/<epic>/phases/<phase>/phase.md`
- `features/<feature>/epics/<epic>/phases/<phase>/tasks/<task>.md`
- `features/<feature>/epics/<epic>/phases/<phase>/report.md`

## Flows

### 1. Init Flow

Preparation and codebase analysis that runs once per target project.

Output:

- `init-summary.md`

This flow is mandatory. It should produce reusable project context that later feature sessions can depend on.

The exact tooling used by this flow is still intentionally open because the framework is expected to incorporate modern MCP servers and external skills over time.

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
- phase folders, `phase.md` files, and empty `tasks/` folders

This flow decides whether the work should proceed directly through phases or first through epics.

### 4. Phase Task Creation Flow

Input:

- the relevant status file
- `phase.md`

Output:

- task markdown files under `tasks/`
- updated `phases.status.json`

The agent first determines what phase it should work on, then gathers codebase and user context, and finally creates the task files and task status entries that will drive implementation.

### 5. Phase Implementation Flow

Input:

- task file paths from `phases.status.json`
- `phase.md`
- current state in `phases.status.json`

Output:

- code changes
- updated task and phase state
- `report.md` when the phase is finished

The agent implements tasks in order by following the indexed task files, updating the relevant status entry after each completed task and marking the phase complete when the phase work is done.

## Current State

This repository is still at an early design stage. The current examples exist to validate the structure, naming, and workflow boundaries before the automation layer is built out.
