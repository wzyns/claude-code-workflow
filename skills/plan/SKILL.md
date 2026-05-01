---
name: plan
description: Third phase of the ccw workflow. Decompose the implementation into well-defined, independently verifiable steps. Captures verification commands (test, lint) on first run into config.json, and creates the feature branch. Use this when the user invokes /ccw:plan directly, or when the orchestrator (/ccw:start) delegates the plan phase.
---

# /ccw:plan — Implementation Plan

Decompose the design into a sequence of small, independently verifiable steps that the `implement` phase will execute.

## Inputs
- The design document (`state.json.config.design_doc_path`, or printed/external)
- `state.json.feature_name`

## Procedure

1. Read state.json and the design document.
2. Decompose the implementation into meaningful steps. Each step should:
   - Have a clear objective
   - Have a bounded scope of changes
   - Be independently verifiable (you should be able to describe the unit test up front)
3. **Ask the user where to save the plan** (default `docs/features/<feature-name>/plan.md`).
4. Write the plan as a markdown document. Suggested structure:
   - Each step gets a heading: `## Step N: <objective>`
   - Under each: scope, files affected, verification method
5. **On first run, ask the user for verification commands** and persist to `config.json`:
   - `test_command` (e.g., `npm test`)
   - `lint_command` (optional, e.g., `npm run lint`)
   - `integration_test_command` (used in `verify` phase)
6. **Branch creation**:
   - Check the project's `CLAUDE.md` and recent `git log` / `git branch` for an established branch-name convention. If found, follow it.
   - Otherwise, propose `feature/<feature-name>` as the default.
   - Confirm the chosen name with the user before running `git checkout -b <branch-name>`.
   - Save to `state.json.config.branch_name`.
7. Ask the user to review the plan. Iterate on feedback.
8. Update state.json:
   - `config.plan_doc_path` = saved path
   - `artifacts.plan_doc` = same
   - `artifacts.implementation_steps` = list of `{step, description, status: "pending"}` derived from the plan headings

## Output
- A markdown plan document
- A new git branch
- `config.json` populated with verification commands

## Exit condition
The user approves the plan.

## What NOT to do
- Don't start implementing — that's `/ccw:implement`.
- Don't combine multiple unrelated changes in a single step.
- Don't create a branch silently — confirm the name with the user first.
- Don't skip persisting verification commands — they are reused throughout the workflow.
