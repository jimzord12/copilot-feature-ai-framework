# Phase

## Goal

- Add a new column to the user table and propagate the change through schema, migration, and application usage.

## Scope

- In scope: schema updates, migration work, and application usage changes for the new column
- Out of scope: unrelated schema cleanup or broad user-domain refactors

## Relevant Context

- Prior decisions from the plan: this phase is a contained schema evolution task for an existing table
- Dependencies on earlier phases or epics: none
- Constraints that matter for this phase: changes should be safe for a brownfield codebase and minimize rollout risk

## Likely Affected Or Created Files

| Path                      | Change Type      | Why It Matters                                  |
| ------------------------- | ---------------- | ----------------------------------------------- |
| user schema or model file | modify           | defines the new column                          |
| migration file            | create or modify | applies the new column in existing environments |
| service or UI files       | modify           | makes use of the new field in application code  |

## Implementation Notes

- Main implementation approach: change the schema source of truth, create the migration, then update application code paths that use the new field
- Important domain or technical details: default behavior and compatibility for existing rows should be considered early
- Sequencing constraints: schema work must precede migration verification and application usage updates

## Dependencies And Blockers

- Dependencies: agreement on the new column shape and semantics
- Possible blockers: uncertain nullability, defaults, or rollout behavior

## Risks

- The new column could require data backfill or compatibility handling for existing records.

## Exit Criteria

1. The user table definition includes the new column.
2. The migration exists and has been verified.
3. Application code paths use the new column correctly.

## Task Strategy

- Keep schema definition, migration verification, and application usage as separate task files.
- Sequence tasks so later implementation builds on verified earlier changes.

## Task Files

| Task ID                         | Task File                                  | Purpose                                           |
| ------------------------------- | ------------------------------------------ | ------------------------------------------------- |
| 001-update-user-schema          | `tasks/001-update-user-schema.md`          | Add the new column to the source-of-truth schema. |
| 002-create-and-verify-migration | `tasks/002-create-and-verify-migration.md` | Create and validate the migration.                |
| 003-update-application-usage    | `tasks/003-update-application-usage.md`    | Update code paths that use the new column.        |

## Notes

- Keep the phase focused on a minimal, safe schema evolution.
