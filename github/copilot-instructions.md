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

### Feature Artifacts

- `brief.md` captures the initial ask.
- `plan.md` captures clarified requirements and implementation strategy.
- `phases.status.json` is used when a feature can proceed directly through phases.
- `epics.status.json` is used when a feature must first be broken into epics.

### Epic Artifacts

- each epic may have its own `brief.md` and `plan.md`
- each epic owns its own `phases.status.json`

### Phase Artifacts

- `phase.md` contains the phase definition and later the task breakdown.
- `report.md` is created or completed after the phase is implemented.

## Status File Rules

Use status files to quickly determine what to work on next.

- `epics.status.json` tracks epics only.
- `phases.status.json` tracks phases and their tasks.
- Valid status values are `not-started`, `in-progress`, and `completed`.
- Keep `id`, `name`, and `path` stable once created.
- Update status entries as work progresses.

Do not move markdown implementation details into the status files. The status files hold state. The markdown files hold the content of the work.

## Selection Logic

When asked to continue work on a feature:

1. Check whether the feature root has `epics.status.json` or `phases.status.json`.
2. If `epics.status.json` exists, select the next epic that is not completed.
3. Inside that epic, read `phases.status.json` and select the next phase that is not completed.
4. Use `phase.md` to understand the work and the task breakdown.
5. Update the relevant status file as tasks and phases move forward.

## Flow Guidance

### Init Flow

- analyze the codebase and gather durable context
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
- create the corresponding folders and `phase.md` files
- do not create `report.md` files until they are actually needed

### Phase Task Creation Flow

- use the status files to choose the active phase
- gather codebase context and ask the user questions if needed
- write a practical task breakdown into `phase.md`
- keep the tasks implementation-oriented and ordered

### Phase Implementation Flow

- implement one task at a time
- update the relevant task state after each completed task
- update the phase state when all tasks are complete
- finish by writing or updating `report.md`

## Style Of The Framework

- prefer direct, practical language
- optimize for team reuse
- avoid unnecessary ceremony
- avoid generic AI-process language when a concrete instruction will do
