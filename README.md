# Microplans

A Claude Code plugin for breaking large, multi-step projects into structured, plannable, and executable units of work.

## The Pattern

The microplan pattern addresses a core challenge in AI-assisted development: large projects exceed what a single context window can handle effectively. Instead of trying to do everything at once, this plugin introduces a delegation-based workflow:

1. An **orchestrator** maintains a master plan with a roadmap of steps
2. A **microplanner agent** researches each step and produces a detailed plan (the "microplan") with sub-steps and outstanding questions
3. Outstanding questions are resolved with the user before implementation begins
4. Implementation is **delegated to a subagent**, keeping the orchestrator's context lean
5. Progress is tracked in the master plan, which survives context loss

Each microplan is a self-contained markdown document stored in `.claude/plans/` that captures the objective, context, research findings, implementation steps, and open questions for a single task.

## When to Use This

This pattern works well for projects where:

- You have a well-defined roadmap with multiple discrete steps
- Each step builds on the previous one
- The total scope would overwhelm a single conversation
- You want structured checkpoints and the ability to resume after context loss

Examples: migrating a codebase to a new framework, implementing a multi-component feature (API + database + frontend + tests), setting up CI/CD pipelines across multiple services, or executing a large refactoring plan.

## Installation

Add the marketplace and install the plugin:

```
/plugin marketplace add computomatic/claude-plugin-microplans
/plugin install microplans@claude-plugin-microplans
```

## Usage

### Starting a roadmap

Invoke the `create-master-plan` skill with a description of your goal:

```
/microplans:create-master-plan Migrate the API from REST to GraphQL
```

The skill will walk you through scoping the work, create a master plan, then execute each step using the plan-question-implement loop.

### Planning a single task

If you just need a detailed plan for one task without the full roadmap workflow, delegate directly to the microplanner agent:

> "Use the microplanner agent to plan out adding rate limiting to the API gateway"

The agent will research the codebase, produce a microplan with implementation steps and outstanding questions, and report back.

## What's Included

| Component | Description |
|---|---|
| **microplanner** agent | Researches a task and produces a structured microplan |
| **create-master-plan** skill | Sets up and executes a multi-step roadmap |
| **using-microplans** skill | Reference for the execution pattern and recovery procedures |
| **writing-microplans** skill | Reference for the microplan document format |
