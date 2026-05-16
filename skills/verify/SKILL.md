---
name: verify
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
4. **If any check fails** (failure path):
   - Diagnose the root cause.
   - Surface the failure and your proposed fix to the user, and ask how to proceed.
   - Apply the fix only after the user agrees.
   - Re-run **all** checks (not just the failing one).
   - If a fix is committed, append a one-line record to `state.json.notes` in the format `"[Verify] commit <short-hash>: <subject>"` (use `git log -1 --pretty='%h %s'`). This record is the input ai-review reads later.
5. When all checks pass (happy path):
   - Print a summary to the user (which checks ran and passed; any fixes applied during this phase).
   - Complete the phase automatically — do not ask for confirmation.

## Output
- Verification report (text summary to the user)
- Code changes if fixes were needed during verification

## Exit condition
All checks pass. On the happy path the phase completes automatically once the summary is printed; on the failure path it completes once the user-approved fixes pass all checks.

## When verification reveals a deeper issue
If a check fails because of a design problem (not just a bug):
1. **Stop** patching around it.
2. Surface the issue to the user.
3. Suggest revising the design or plan (`/ccw:design` or `/ccw:plan`).

## What NOT to do
- Don't silence or skip a failing check to "make it green".
- Don't re-implement features here — fix integration-level issues only.
- Don't use `--no-verify` or similar bypasses on git or test hooks.
