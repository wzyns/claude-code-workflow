# Verify Phase

Skill: `/ccw:verify`

Run integration-level checks against the complete implementation.

## Input

- The completed implementation
- `state.json.config.integration_test_command`, `lint_command`, etc.

## Behavior

1. Run the integration test command (`integration_test_command`).
2. Run additional checks such as lint and type check.
3. **Happy path**: when every check passes, print the summary and complete the phase automatically — no user confirmation prompt.
4. **Failure path**: when any check fails, diagnose the root cause, surface it to the user along with the proposed fix, and ask how to proceed. Apply the fix only after the user agrees, then re-verify.

## Output

- Verification report (summary of which checks passed/failed and any fixes applied)
- Any fix committed during verify is appended to `state.json.notes` as `"[Verify] commit <short-hash>: <subject>"` so later phases (notably `ai-review`) can locate the changes that belong to this workflow

## Exit Condition

- All checks pass — the phase completes automatically. On the failure path, the phase completes once the user-approved fixes pass all checks.

## Notes

- This phase is for *integration*-level confidence. Per-step unit tests are already done in `implement`.
- If verification reveals a deeper design issue, surface it explicitly rather than patching around it.
