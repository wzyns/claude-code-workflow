---
description: Fifth phase of the ccw workflow. Run integration-level checks against the complete implementation — integration tests, full test suite, lint, type check — and diagnose/fix failures. Use this when the user invokes /ccw:verify directly, or when the orchestrator (/ccw:start) delegates the verify phase.
---

# /ccw:verify — Integration Verification

Run integration-level checks against the complete implementation. Per-step unit tests are already done in `implement`; this phase is for *integration* confidence.

## Inputs
- `state.json.config.integration_test_command`
- `state.json.config.lint_command` (optional)
- The completed implementation on the feature branch

## Procedure

1. Read state.json.
2. **Run the integration test command**. If `integration_test_command` is missing, ask the user how integration tests are run, and offer to save the command to `config.integration_test_command`.
3. **Run lint, type check, and any other quality gates** the project uses. The full test suite (not just unit tests) should also run here if it differs from the integration command.
4. **If any check fails**:
   - Diagnose the root cause
   - Fix it (or surface to the user if the fix isn't obvious)
   - Re-run **all** checks (not just the failing one)
5. When all checks pass, summarize for the user:
   - Which checks ran and passed
   - Any fixes applied during this phase
6. Confirm with the user that verification is complete.
7. Update state.json `updated_at`.

## Output
- Verification report (text summary to the user)
- Code changes if fixes were needed during verification

## Exit condition
All checks pass and the user confirms.

## When verification reveals a deeper issue
If a check fails because of a design problem (not just a bug):
1. **Stop** patching around it.
2. Surface the issue to the user.
3. Suggest revising the design or plan (`/ccw:design` or `/ccw:plan`).

## What NOT to do
- Don't silence or skip a failing check to "make it green".
- Don't re-implement features here — fix integration-level issues only.
- Don't use `--no-verify` or similar bypasses on git or test hooks.
