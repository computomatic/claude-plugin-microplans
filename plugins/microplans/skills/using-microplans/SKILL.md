---
name: using-microplans
description: Reference for the microplan execution pattern. Describes the master plan structure, the plan-question-implement loop, and recovery procedures. Consult this when orchestrating multi-step roadmaps using microplans.
---

# Using Microplans

The microplan pattern breaks large roadmaps into small, self-contained steps that are planned and executed one at a time. A master plan tracks overall progress and serves as a resilient checkpoint so work can resume after context loss. Each step gets its own microplan document with a detailed implementation plan and outstanding questions, which are resolved with the user before implementation begins.

## Rules

These rules are non-negotiable. They apply to every step, regardless of perceived complexity.

- **Never plan directly.** Always delegate planning to the microplanner agent using the Agent tool. Even if a step seems trivial, the microplanner must produce the microplan. The orchestrator must never write microplan documents itself.
- **Never implement directly.** Always delegate implementation to a subagent (e.g., a lead-developer agent) using the Agent tool. The orchestrator must never edit code, run tests, run formatters, or run linters itself.
- **Why this matters.** The orchestrator's context window is a shared, limited resource across the entire roadmap. Every file read, code edit, test run, or lint fix performed directly in the orchestrator consumes context needed for coordination. Delegation to subagents is not a suggestion -- it is the mechanism that makes multi-step roadmaps feasible. Each subagent gets its own context window, keeping the orchestrator lean.

## The Master Plan

The master plan is the source of truth for the entire roadmap. Create it using plan mode (EnterPlanMode) at the start of a new roadmap. It must be detailed enough that a fresh session with no prior context can pick up where work left off.

Key properties:

- Created and managed via native plan mode (EnterPlanMode)
- Each step has a status: `pending`, `in-progress`, or `done`
- Updated after every step completes
- Contains enough context that reading it alone is sufficient to continue work
- Must include a full copy of the execution loop process so a fresh session can resume autonomously from the plan alone

## The Loop

Execute the roadmap by repeating this cycle for each step:

1. **Review the master plan.** Read the master plan file. Identify the next `pending` step. Mark it `in-progress`.

2. **Delegate planning to the microplanner agent (mandatory).** Use the Agent tool to delegate to the microplanner. Do not write the microplan yourself, regardless of how simple the step appears. Pass the agent the full context it needs since it runs in an isolated context window and will not read the master plan itself. Include: the step's title and description from the master plan, any relevant decisions or constraints from prior steps, and an explicit domain role assignment (e.g., "You are planning a database migration step" or "You are planning a frontend accessibility audit"). The domain role helps the agent adopt the right perspective and terminology.

3. **Review the microplan and ask questions.** Read the microplan the agent created. Present Outstanding Questions to the user **one at a time -- ask a single question, wait for the answer, then ask the next**. Never present multiple questions in one message.

4. **Incorporate answers.** Once all questions are answered, delegate back to a subagent to update the microplan with the user's answers. The subagent should revise the Implementation Plan to reflect the decisions and clear the answered questions from Outstanding Questions.

5. **Delegate implementation to a subagent (mandatory).** Use the Agent tool to send the finalized microplan to an implementation agent from the Available Agents section (e.g., a lead-developer agent). Do not implement changes directly in this conversation -- no code edits, no test runs, no formatting. Pass the full microplan content so the agent has everything it needs.

6. **Clean up and update.** Delete the microplan file. Update the master plan: mark the completed step as `done` and note any outcomes or decisions that affect future steps.

7. **Commit and push.** Commit all changes from this step with a clear message referencing the step. Push to the remote.

8. **Loop.** Return to step 1 for the next pending step. If all steps are `done`, the roadmap is complete.

## Master Plan Format

Use this structure when creating a master plan:

```markdown
# [Project/Feature Name]

**Plan file:** `[absolute path to this file]`

## Context

[Background information, constraints, and key decisions that apply across all steps.]

## Available Agents

[List each relevant subagent with its name and what types of tasks it handles.]

- **microplanner**: Researches and writes detailed microplans for individual steps. Always used for planning.
- **[agent-name]**: [When to use this agent for implementation tasks.]

## Roadmap

### Step 1: [Title] - `done`
[Brief description of what this step accomplishes.]
**Outcome:** [Filled in after completion. Key decisions made, files changed, anything the next step needs to know.]

### Step 2: [Title] - `in-progress`
[Brief description of what this step accomplishes.]

### Step 3: [Title] - `pending`
[Brief description of what this step accomplishes.]

## Rules

These rules are non-negotiable. They apply to every step, regardless of perceived complexity.

- **Never plan directly.** Always delegate planning to the microplanner agent using the Agent tool. The orchestrator must never write microplan documents itself.
- **Never implement directly.** Always delegate implementation to a subagent using the Agent tool. The orchestrator must never edit code, run tests, run formatters, or run linters itself.
- **Why this matters.** The orchestrator's context window is a shared, limited resource across the entire roadmap. Every file read, code edit, test run, or lint fix performed directly in the orchestrator consumes context needed for coordination. Delegation is the mechanism that makes multi-step roadmaps feasible.

## Execution Process

For each pending step, repeat this cycle:

1. **Review the master plan.** Identify the next `pending` step. Mark it `in-progress`.
2. **Delegate planning (mandatory).** Use the Agent tool to delegate to the microplanner agent. Never write the microplan yourself. Pass full context: the step's title and description, relevant decisions from prior steps, and an explicit domain role assignment. The agent runs in an isolated context and will not read this plan.
3. **Review the microplan and ask questions.** Present Outstanding Questions to the user **one at a time -- ask a single question, wait for the answer, then ask the next**. Never present multiple questions in one message.
4. **Incorporate answers.** Delegate to a subagent to update the microplan with the user's answers.
5. **Delegate implementation (mandatory).** Use the Agent tool to send the finalized microplan to an implementation agent from the Available Agents section. Never implement directly. Pass the full microplan content.
6. **Clean up and update.** Delete the microplan file. Mark the step `done` and note outcomes.
7. **Commit and push.**
8. **Loop.** Return to sub-step 1. If all steps are `done`, the roadmap is complete.

### Guidelines

- **One question at a time.** When presenting Outstanding Questions, always ask exactly one question per message. Wait for the user's answer before asking the next. Never batch multiple questions into a single message.
- If a session ends mid-work, read this plan to resume. The `in-progress` step is where to pick up.
- Each step should produce a complete, working state.
- If new requirements surface, revise this plan before continuing.
```

## Guidelines

- **One question at a time.** When presenting Outstanding Questions, always ask exactly one question per message. Wait for the user's answer before asking the next. Never batch multiple questions into a single message.

- **Recovery from context loss.** If a session ends mid-work, start the next session by reading the master plan. The `in-progress` step tells you where to pick up. If a microplan file exists for that step, resume from there. If not, re-run the microplanner.

- **Keep steps self-contained.** Each step should produce a complete, working state. Avoid steps that leave things broken or that depend on unfinished work from a future step.

- **Master plan is the source of truth.** Do not rely on conversation history for roadmap state. Always read the master plan to determine what has been done and what comes next.

- **Adapt the loop.** If a step surfaces new requirements, pause and revise the master plan before continuing.
