I want to build a practical framework for organising my AI Agents when it comes to feature development.

The framework is meant for brownfield projects and feature development only! Our objective is not complete project or app development. For now we will only support vscode copilot as the AI agent, but we will design the framework in a way that it can be easily extended to support other agents in the future. So for the 1st version we will focus on using the copilot primitives like:

- custom instructions (github/copilot-instructions.md)
- agent files (github/agents) - \*.agent.md
- prompts (github/prompts) - \*.prompt.md
- skills (github/skills/<skill-name>/SKILL.md)

The main idea is that there will be 5 main flows.

- The **init flow** (preperation + codebase analysis), is executed once
- The **requirements generation flow**, this requires a `brief.md` + super AI grilling (ts wizard's /grill-me skill) so all logic branches are clearly defined (This should run once per feature or in case of a big feature, once per epic). This step generates the PLAN.md (previously called OVERVIEW.md) which is a detailed document that contains the requirements and the implementation details for the feature. This document is then used in the "phase prep" flow to create the phases.
- The **phase generation flow**, this is where the agent reads the PLAN.md and creates the different phases of the implementation. The output of this phase is a `status.json` file that contains the list of phases, their status (not started, in progress, completed). This flow also creates the phase folders and files (not the report.md ones, these are created after the phase is completed). The phase.md file contains the implementation details for the phase, (not the the tasks at this point) to be done and any other relevant information that will be used in the "phase prep" flow. A template for the phase.md file will be available at /templates/phase.template.md
- The **phase task creation flow**, this where the agent reads that `status.json` to figure out which phase to select, then it reads the phase to understand what needs to be done, next collects context from the codebase and the user if needed (using the built-in ask questions tool), afterwards it breaks down the phase into Tasks. These live in the phase.md file.
- The **phase implementation flow**, this is the most straightforward flow, the agent reads the tasks from the `phase.md` file and implements them one by one. After each task is completed, the agent updates the related `status.json` file and moves on to the next task until all tasks are completed. Once all tasks are completed, the agent marks the phase as completed in the `status.json` file.

> Note: Every flow should be run on a fresh session so the agent's context window is empty.

Additionally, templates for the different documents will be available in the `/templates` folder, and the agent will use them to create the different documents needed for each flow. This will ensure consistency across all features and phases.
