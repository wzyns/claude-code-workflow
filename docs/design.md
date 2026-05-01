# Claude Code Feature Development Workflow Skill — Design Document

## 1. Goal and Background

This project formalizes the recurring process used to develop a single feature with Claude Code into a structured skill. Instead of the user manually instructing each step, the skill drives the workflow proactively while the user only needs to respond and review at each stage. The goal is a "development framework" experience where Claude leads and the user participates with answers and approvals.

## 2. Workflow Phases

Feature development consists of the following 8 phases.

| Phase | Name | Description |
|-------|------|-------------|
| 1 | Conversational Design (`design`) | Design the feature through dialogue with the user |
| 2 | Design Documentation (`document`) | Write the agreed-upon design as a markdown document |
| 3 | Implementation Plan (`plan`) | Decompose the implementation into well-defined steps |
| 4 | Step-by-Step Implementation (`implement`) | For each step: [implement → review → unit test], iterating |
| 5 | Integration Verification (`verify`) | Run integration tests and other checks against the full implementation |
| 6 | AI Review (`ai-review`) | AI-driven code review (e.g., `/ultrareview`) |
| 7 | Final User Review (`user-review`) | The user personally reviews all changes |
| 8 | Pull Request (`pr`) | Create the PR, including any noteworthy development notes as PR comments |

## 3. Design Decisions

| Item | Decision | Notes |
|------|----------|-------|
| Skill structure | Hybrid (orchestrator + per-phase sub-skills) | Each phase can be invoked independently and reused |
| Progress state storage | `.claude/workflow/<feature-name>/state.json` | Local metadata; recommended to be gitignored |
| Artifact storage (default) | `docs/features/<feature-name>/` | Treats design and plan documents as git-tracked assets |
| Artifact location prompt | Asked each time | Allows external destinations such as Confluence |
| Autonomy level | User confirmation at every phase transition | Ensures the user can add input before progressing |
| AI review tool | Instructs the user to run `/ultrareview` | The skill cannot invoke `/ultrareview` itself |
| PR creation tool | `gh pr create` | Uses the standard Claude Code PR protocol |
| Per-phase verification commands | Asked once on first run, stored in `config.json` | Reused across the same feature |

## 4. Skill Structure

### 4.1 Orchestrator

The `/feature` skill orchestrates the entire workflow.

- Reads `state.json` to determine the current phase
- Confirms with the user before transitioning to the next phase
- Invokes the appropriate sub-skill via the `Skill` tool
- Updates `state.json` upon completion of each phase

### 4.2 Per-Phase Sub-skills

Each phase is implemented as an independent skill file.

- `feature-design`
- `feature-document`
- `feature-plan`
- `feature-implement`
- `feature-verify`
- `feature-ai-review`
- `feature-user-review`
- `feature-pr`

Each sub-skill is responsible for:

- Validating the inputs it needs (state.json, prior artifacts, etc.)
- Executing its phase
- Persisting outputs and updating `state.json`
- Returning control to the caller (orchestrator or user) after completion

## 5. Directory and File Layout

### 5.1 Skill Directory

```
.claude/skills/
├── feature/SKILL.md
├── feature-design/SKILL.md
├── feature-document/SKILL.md
├── feature-plan/SKILL.md
├── feature-implement/SKILL.md
├── feature-verify/SKILL.md
├── feature-ai-review/SKILL.md
├── feature-user-review/SKILL.md
└── feature-pr/SKILL.md
```

### 5.2 Per-Feature Workflow Working Directory

```
.claude/workflow/<feature-name>/
├── state.json    # Progress state
└── config.json   # Verification commands and other settings
```

### 5.3 Artifact Directory (Default)

```
docs/features/<feature-name>/
├── design.md
└── plan.md
```

> When the user chooses an external destination (e.g., Confluence), the skill outputs the markdown body and the user publishes it to the external system. The external location (URL, description, etc.) is recorded in `state.json` under `design_doc_external`.

## 6. Invocation

### 6.1 Main Entry Point

```
/feature
```

The orchestrator performs the following:

1. Searches the current directory for an in-progress workflow (state.json)
2. If found → asks the user "Which feature do you want to resume?" or "Start a new one?"
3. If not found → asks for the feature name, artifact location, and other initial information, then begins phase 1

### 6.2 Direct Sub-skill Invocation

```
/feature-design
/feature-document
/feature-plan
/feature-implement
/feature-verify
/feature-ai-review
/feature-user-review
/feature-pr
```

Use these when re-running a single phase. The invoked sub-skill uses the `state.json` in the current workflow directory as context.

## 7. State Management (state.json)

### 7.1 Schema

```json
{
  "feature_name": "user-auth",
  "started_at": "2026-05-01T10:00:00Z",
  "updated_at": "2026-05-01T15:30:00Z",
  "current_phase": "implement",
  "completed_phases": ["design", "document", "plan"],
  "config": {
    "design_doc_path": "docs/features/user-auth/design.md",
    "design_doc_external": null,
    "plan_doc_path": "docs/features/user-auth/plan.md",
    "test_command": "npm test",
    "lint_command": "npm run lint",
    "integration_test_command": "npm run test:integration",
    "branch_name": "feature/user-auth"
  },
  "artifacts": {
    "design_doc": "docs/features/user-auth/design.md",
    "plan_doc": "docs/features/user-auth/plan.md",
    "implementation_steps": [
      { "step": 1, "description": "Define schema", "status": "done" },
      { "step": 2, "description": "API endpoints", "status": "in_progress" },
      { "step": 3, "description": "Frontend integration", "status": "pending" }
    ]
  },
  "notes": []
}
```

### 7.2 Phase Values

- `design`, `document`, `plan`, `implement`, `verify`, `ai-review`, `user-review`, `pr`, `done`

### 7.3 implementation_steps Status Values

- `pending`, `in_progress`, `done`

## 8. Per-Phase Behavior

### 8.1 design (Conversational Design)

- **Input:** User's feature idea (free-form dialogue)
- **Behavior:** Through conversation, clarify requirements, constraints, alternatives, and trade-offs. Accumulate agreed points as notes.
- **Output:** Summary of the agreed design (accumulated in `state.json.notes` or as scratch notes)
- **Exit condition:** The user signals "design complete" or otherwise approves moving on.

### 8.2 document (Design Documentation)

- **Input:** Agreements from the design phase
- **Behavior:**
  1. Ask for the storage location (default `docs/features/<name>/design.md`, or external)
  2. Write the markdown document
  3. Ask the user to review and incorporate feedback
- **Output:** `design.md` (or an external destination)
- **Exit condition:** The user approves the document.

### 8.3 plan (Implementation Plan)

- **Input:** Design document
- **Behavior:**
  1. Decompose the implementation into meaningful steps (each independently verifiable)
  2. For each step, specify objective, scope of changes, and verification method
  3. Ask the user to review and approve
  4. On first run, ask for verification commands (`test`, `lint`, etc.) and persist to `config.json`
  5. Create a branch (decide `branch_name` and run `git checkout -b`)
- **Output:** `plan.md`, `config.json`, new branch
- **Exit condition:** The user approves the plan.

### 8.4 implement (Step-by-Step Implementation)

- **Input:** Implementation plan
- **Behavior** (repeat per `implementation_step`):
  1. Implement the code for the step
  2. Self-review the step's output
  3. Write and run unit tests
  4. Report the step's result to the user and confirm progression to the next step
- **Output:** Code changes, tests, updated `implementation_steps[].status` in `state.json`
- **Exit condition:** All steps are `done`.

### 8.5 verify (Integration Verification)

- **Input:** Completed implementation
- **Behavior:**
  1. Run the integration test command (`integration_test_command`)
  2. Run additional checks such as lint and type check
  3. On failure, diagnose, fix, and re-verify
- **Output:** Verification report
- **Exit condition:** All checks pass and the user confirms.

### 8.6 ai-review (AI Review)

- **Input:** Completed implementation (preferably committed)
- **Behavior:**
  1. Ask the user to run `/ultrareview`
  2. When the user shares the results, summarize and organize them
  3. Decide together which items to address vs. ignore
  4. Apply code changes for items to address
- **Output:** Code changes addressing review feedback
- **Exit condition:** The user confirms the AI review feedback has been handled.

### 8.7 user-review (Final User Review)

- **Input:** Implementation including AI review changes
- **Behavior:**
  1. Present a summary of changes to the user (diff summary, scope of impact, etc.)
  2. Give the user time to review directly
  3. Apply additional changes based on user feedback
- **Output:** Final code
- **Exit condition:** The user approves moving to PR creation.

### 8.8 pr (Pull Request)

- **Input:** Final code
- **Behavior:**
  1. Follow the Claude Code PR protocol
     - Analyze changes, draft title and body
     - On user approval, run `git push` and `gh pr create`
  2. Add any notable development notes (trade-offs, known limitations, follow-ups) as PR comments
- **Output:** PR URL
- **Exit condition:** PR is created; `state.json.current_phase` is set to `done`.

## 9. Phase Transition Rules

- The user must confirm every phase transition.
- At each transition prompt, the skill explicitly invites the user to add input or request changes.
- If the user requests rolling back to a previous phase, the orchestrator rewinds `current_phase` and resumes from that phase.

## 10. Open Items and Future Extensions

- Concurrent multi-feature support (currently assumes one in-progress feature at a time)
- Schema versioning for `state.json` (migration strategy when the schema evolves)
- Direct integration with external systems such as Confluence (currently the user publishes manually)
- Workflow restart and rollback commands (`/feature reset`, `/feature back`, etc.)
- Optional retrospective phase after a feature is shipped
