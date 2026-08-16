# Architecture

All skills live inside a single Claude Code plugin named `ccw`. Plugin namespacing means each skill folder `<name>/` is invoked as `/ccw:<name>`.

## Orchestrator

The `start` skill (invoked as `/ccw:start`) orchestrates the entire workflow.

- Reads `state.json` to determine the current phase
- Confirms with the user before transitioning to the next phase
- Invokes the appropriate sub-skill via the `Skill` tool
- Updates `state.json` upon completion of each phase

See [phases/start.md](phases/start.md) for the orchestrator's detailed behavior.

## Per-Phase Sub-skills

Each phase is implemented as an independent skill folder under `skills/`.

| Skill folder | Invoked as | Phase | Spec |
|---|---|---|---|
| `design` | `/ccw:design` | Conversational design + design documentation | [phases/design.md](phases/design.md) |
| `plan` | `/ccw:plan` | Implementation plan | [phases/plan.md](phases/plan.md) |
| `implement` | `/ccw:implement` | Step-by-step implementation | [phases/implement.md](phases/implement.md) |
| `ai-review` | `/ccw:ai-review` | AI review | [phases/ai-review.md](phases/ai-review.md) |
| `user-review` | `/ccw:user-review` | Final user review | [phases/user-review.md](phases/user-review.md) |
| `pr` | `/ccw:pr` | Pull request creation | [phases/pr.md](phases/pr.md) |

Each sub-skill is responsible for:

- Validating the inputs it needs (state.json, prior artifacts, etc.)
- Executing its phase
- Persisting outputs and updating `state.json`
- Returning control to the caller (orchestrator or user) after completion

## Invocation

### Main Entry Point

```
/ccw:start
```

The orchestrator performs the following:

1. Searches the current directory for an in-progress workflow (state.json)
2. If found → asks the user "Which feature do you want to resume?" or "Start a new one?"
3. If not found → asks for the feature name, artifact location, and other initial information, then begins phase 1

### Direct Sub-skill Invocation

```
/ccw:design
/ccw:plan
/ccw:implement
/ccw:ai-review
/ccw:user-review
/ccw:pr
```

Use these when re-running a single phase. The invoked sub-skill uses the `state.json` in the current workflow directory as context.
