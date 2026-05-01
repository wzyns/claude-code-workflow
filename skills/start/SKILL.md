---
name: start
description: Orchestrate the ccw feature development workflow. Walks the user through 8 phases (design → document → plan → implement → verify → ai-review → user-review → pr), invokes the corresponding sub-skill for each phase, manages state in .claude/ccw/<feature>/state.json, and confirms every phase transition with the user. Use this when the user says "start a new feature", "begin a workflow", or runs /ccw:start.
---

# /ccw:start — Orchestrator

You are the orchestrator for the `ccw` feature development workflow. Your job is to walk the user through 8 phases of feature development. **Real work happens inside per-phase sub-skills**; you sequence them, manage shared state, and confirm transitions with the user.

## Phase order

```
design → document → plan → implement → verify → ai-review → user-review → pr → done
```

Each phase corresponds to a sub-skill: `ccw:design`, `ccw:document`, `ccw:plan`, etc.

## Step 1 — Detect existing workflows

1. Find every `.claude/ccw/<feature-name>/state.json` in the current project.
2. Classify each entry:
   - **in-progress** — file parses as JSON and `current_phase` is a non-`"done"` value
   - **done** — file parses as JSON and `current_phase == "done"`
   - **broken** — file is missing, unreadable, unparseable, or has no usable `current_phase`
3. **Silently drop done entries.** Never mention them to the user.
4. If any **broken** entries exist, print a single warning line listing their paths — e.g.,
   `Warning: skipped N broken workflow state(s): <path1>, <path2>`.
   Do **not** offer them for resume.
5. If one or more **in-progress** entries exist, list them with their `feature_name` and `current_phase`, then ask:
   - "Resume one of these?" (let them pick by name)
   - "Or start a new feature?"
6. If zero **in-progress** entries remain, proceed straight to Step 2 without prompting. Do not say "no workflows found" when only done workflows exist — just move on.

## Step 2 — Start a new feature (if applicable)

1. Ask the user for:
   - **Feature name** (kebab-case, e.g., `user-auth`). This becomes the workflow directory name.
   - **One- or two-sentence description** of the feature.
2. Create `.claude/ccw/<feature-name>/state.json` with:
   ```json
   {
     "feature_name": "<name>",
     "started_at": "<ISO 8601 timestamp now>",
     "current_phase": "design",
     "completed_phases": [],
     "config": {},
     "artifacts": {},
     "notes": ["<the description the user gave>"]
   }
   ```
3. Proceed to Step 3.

## Step 3 — Orchestration loop

Repeat until `current_phase == "done"`:

1. **Read** `state.json`.
2. If `current_phase == "done"`:
   - Summarize the completed feature (what was designed, what shipped, PR URL if any).
   - Exit.
3. **Invoke** the sub-skill for `current_phase` using the `Skill` tool with skill name `ccw:<current_phase>`.
4. When the sub-skill returns:
   a. Re-read `state.json` (the sub-skill may have updated it).
   b. If the sub-skill reports its phase is complete, update `state.json`:
      - Push `current_phase` into `completed_phases`
      - Set `current_phase` to the next phase in the order
   c. **Summarize** what just completed (the artifact produced, key decisions, any notes).
   d. **Ask the user**:
      - "Anything you'd like to revise before moving on to `<next-phase>`?"
      - "Confirm proceeding to `<next-phase>`? (yes / no / back)"
   e. Honor the user's response:
      - `yes` → continue the loop
      - `no` or feedback → re-invoke the same sub-skill or address the feedback
      - `back` → rewind `current_phase` to the previous phase, remove it from `completed_phases`, and re-invoke that sub-skill

## State management rules

- **Never** perform phase work yourself. Delegate to the sub-skill.
- If a sub-skill reports incomplete or fails, leave `current_phase` unchanged and surface the failure to the user.
- If `state.json` is malformed when reading, surface the issue to the user before doing anything destructive.

## User confirmation is mandatory at every transition

The user may have additional input at any phase boundary. Always pause and ask before advancing — even if the sub-skill's output looks complete. Do not chain into the next phase silently.

## Stopping mid-workflow

If the user indicates they want to pause (e.g., "stop", "I'll come back later"):

1. Make sure `state.json` reflects the latest progress.
2. Confirm where state is saved (path under `.claude/ccw/<feature-name>/`).
3. Tell the user how to resume: "Run `/ccw:start` again and select this feature."
4. Exit.
