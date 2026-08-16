---
name: plan
description: Second phase of the ccw workflow. Decompose the implementation into well-defined, independently verifiable steps, save the plan to .claude/ccw/<feature>/plan.md, and create the feature branch. Use this when the user invokes /ccw:plan directly, or when the orchestrator (/ccw:start) delegates the plan phase.
---

# /ccw:plan — Implementation Plan

Decompose the design into a sequence of small, independently verifiable steps that the `implement` phase will execute.

## Inputs
- The design document (`state.json.config.design_doc_path`)
- `state.json.feature_name`

## Procedure

1. Read state.json and the design document.
2. Decompose the implementation into meaningful steps. Each step should:
   - Have a clear objective
   - Have a bounded scope of changes
   - Be independently verifiable (you should be able to describe the unit test up front)
3. Draft the plan as a markdown document. Suggested structure:
   - Each step gets a heading: `## Step N: <objective>`
   - Under each: scope, files affected, verification method
4. **Present an abbreviated summary in chat** for the user to review:
   - List each step as `## Step N: <objective>` — heading and objective only.
   - Do NOT dump per-step scope, affected files, or verification details into chat. Those live only in the saved document.
   - Tell the user the full detail will be persisted to the document, and offer to expand any specific step on request.
5. **Iterate until approved**: collect the user's feedback, revise the plan, and re-present the abbreviated summary. Repeat until the user explicitly approves (e.g., "looks good", "approved", "let's save it"). Do not proceed past this step on implicit silence.
6. After approval, save the plan to `.claude/ccw/<feature-name>/plan.md`. **Do not ask where to save it** — the local default path is always used. No need to surface the file path in chat — the user already reviewed the content.
7. **Branch creation**:
   - Check the project's `CLAUDE.md` and recent `git log` / `git branch` for an established branch-name convention. If found, follow it.
   - Otherwise, propose `feature/<feature-name>` as the default.
   - Confirm the chosen name with the user before running `git checkout -b <branch-name>`.
   - Save to `state.json.config.branch_name`.
8. Update state.json:
    - `config.plan_doc_path` = `.claude/ccw/<feature-name>/plan.md`
    - `artifacts.plan_doc` = same value
    - `artifacts.implementation_steps` = list of `{step, description, status: "pending"}` derived from the plan headings

## Output
- A markdown plan document saved at `.claude/ccw/<feature-name>/plan.md`
- A new git branch

## Exit condition
The plan is approved by the user and saved, and the feature branch is created.

## What NOT to do
- Don't start implementing — that's `/ccw:implement`.
- Don't combine multiple unrelated changes in a single step.
- Don't create a branch silently — confirm the name with the user first.
- Don't save the plan before the user has approved its content.
- Don't ask where to save the plan — always use the default local path.
- Don't report the saved file path back to the user — they reviewed the content directly, so the location adds no value.
