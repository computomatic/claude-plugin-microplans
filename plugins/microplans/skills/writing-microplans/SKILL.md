---
name: writing-microplans
description: Reference for the microplan document format. Provides structure, naming conventions, and writing guidelines for creating microplan files. Use when creating microplan documents.
allowed-tools: Bash(echo *)
---

# Writing Microplans

This document defines the standard format for microplan files. A microplan is a self-contained unit of work: it captures everything needed to understand, plan, and implement a single focused task.

## File Location and Naming

**Microplan directory:** `!`echo "${TMPDIR:-/tmp}/claude-microplans/"``

- Store microplans in the directory shown above
- Create the directory with `mkdir -p` before writing if it does not exist
- Prefix filenames with `microplan-`
- Use descriptive kebab-case names that reflect the task's purpose
- Examples: `microplan-add-auth-middleware.md`, `microplan-refactor-database-layer.md`

## Document Structure

Use this template when writing a microplan:

```markdown
# Microplan: [Title]

## Objective

[One or two sentences describing what this task accomplishes and why.]

## Context

[Relevant background: what motivated this work, any prior decisions or constraints that affect the approach, and anything else needed to understand the task from scratch.]

## Research Findings

[Summary of what you learned from all research sources: codebase exploration, web searches, documentation, and any resources suggested in the delegation prompt. Include relevant details, existing patterns, dependencies, and anything that influences the implementation approach.]

## Implementation Plan

1. [First sub-step with specific details]
2. [Second sub-step]
3. [Continue as needed]

Each sub-step should be small enough to implement without further decomposition.

## Outstanding Questions

- [ ] [Question about an uncertain decision or missing context]
- [ ] [Question about a consequential choice that the user should weigh in on]
```

## Writing Guidelines

- **Research thoroughly** before writing the plan. Use all available sources: read relevant source files, run web searches for documentation or APIs, and consult any resources suggested in the delegation prompt. Understand existing patterns and trace through the areas that will be affected.
- **Be specific.** Reference exact names, locations, and details relevant to the domain. A good microplan leaves no ambiguity about what to do or where to do it.
- **Ask questions for uncertainty.** If you are unsure about a design decision, an edge case, or a requirement, add it to Outstanding Questions rather than guessing. The user is the domain expert.
- **Ask questions for consequential decisions.** Even if you have a reasonable default in mind, flag choices that are hard to reverse or that significantly shape the approach. Let the user confirm or redirect.
- **Focus on a single task.** Each microplan covers exactly one cohesive unit of work. Do not bundle unrelated changes or let scope creep in.
- **Keep sub-steps concrete and ordered.** Each sub-step in the Implementation Plan should describe a single, actionable change. Order them so each builds on the previous one.
