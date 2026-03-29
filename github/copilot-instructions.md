# Copilot Instructions For This Framework

This repository defines a framework for AI-assisted feature delivery.

Treat the framework as:

- brownfield-only
- feature-development-only
- Copilot-first
- intentionally lightweight

Do not expand the workflow into a full project management system.

## Working Rules

- Prefer the smallest artifact set that keeps the workflow clear.
- Keep machine-friendly state in JSON status files.
- Keep planning, rationale, requirements, and task details in markdown files.
- Do not create extra tracking documents unless explicitly requested.
- Treat every flow as a fresh-session workflow. Do not rely on earlier chat context when durable files should be updated instead.

## Artifact Rules

### Project Artifact

- `init-summary.md` is mandatory.
- It is the durable output of the init flow and should be created once per target codebase.
- Later flows should consult it before rediscovering project context.

### Feature Artifacts

- `brief.md` captures the initial ask.
- `plan.md` captures clarified requirements and implementation strategy.
- `phases.status.json` is used when a feature can proceed directly through phases.
- `epics.status.json` is used when a feature must first be broken into epics.

### Epic Artifacts

- each epic may have its own `brief.md` and `plan.md`
- each epic owns its own `phases.status.json`

### Phase Artifacts

- `phase.md` contains the stable phase definition, context, and task index.
- `tasks/*.md` contains the actionable implementation tasks for that phase.
- `report.md` is created or completed after the phase is implemented.

## Status File Rules

Use status files to quickly determine what to work on next.

- `epics.status.json` tracks epics only.
- `phases.status.json` tracks phases and their task files.
- Valid status values are `not-started`, `in-progress`, and `completed`.
- Keep `id`, `name`, `path`, and `summary` stable once created unless there is a deliberate re-plan.
- Update status entries as work progresses.

Do not move markdown implementation details into the status files. The status files hold state. The markdown files hold the content of the work.

For `phases.status.json`, each phase entry should contain:

- `id`
- `name`
- `path`
- `summary`
- `status`
- `tasks`

Each task entry should contain:

- `id`
- `name`
- `path`
- `summary`
- `status`

Do not create a separate `tasks.status.json` unless the framework grows enough that task state can no longer be managed reasonably inside `phases.status.json`.

## Selection Logic

When asked to continue work on a feature:

1. Check whether the feature root has `epics.status.json` or `phases.status.json`.
2. If `epics.status.json` exists, select the next epic that is not completed.
3. Inside that epic, read `phases.status.json` and select the next phase that is not completed.
4. Use `phase.md` to understand the phase context and find the relevant task files.
5. Update the relevant status file as tasks and phases move forward.

## Flow Guidance

### Init Flow

- analyze the codebase and gather durable context
- create or update `init-summary.md`
- prefer MCP servers, skills, and reusable tooling when they reduce repeated analysis
- keep this flow practical and avoid speculative artifacts

### Requirements Generation Flow

- start from `brief.md`
- challenge unclear assumptions and edge cases by relentessly grilling the user about the feature intent and requirements.
- produce a concrete `plan.md`
- the resulting plan should be detailed enough to support phase generation without re-deriving requirements

### Phase Generation Flow

- read `plan.md`
- decide whether the work should be tracked by phases directly or by epics first
- create the appropriate status file
- create the corresponding folders, `phase.md` files, and empty `tasks/` folders
- do not create `report.md` files until they are actually needed

### Phase Task Creation Flow

- use the status files to choose the active phase
- gather codebase context and ask the user questions if needed
- create a practical set of task files under `tasks/`
- update `phases.status.json` with task entries that point to those files
- keep the tasks implementation-oriented and ordered

### Phase Implementation Flow

- implement one task at a time
- use the task file referenced in `phases.status.json` as the unit of work
- update the relevant task state after each completed task
- update the phase state when all tasks are complete
- finish by writing or updating `report.md`

## Style Of The Framework

- prefer direct, practical language
- optimize for team reuse
- avoid unnecessary ceremony
- avoid generic AI-process language when a concrete instruction will do
