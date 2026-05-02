# Verify Phase

Skill: `/ccw:verify`

Run integration-level checks against the complete implementation.

## Input

- The completed implementation
- `state.json.config.integration_test_command`, `lint_command`, etc.

## Behavior

1. Run the integration test command (`integration_test_command`).
2. Run additional checks such as lint and type check.
3. On failure, diagnose, fix, and re-verify.

## Output

- Verification report (summary of which checks passed/failed and any fixes applied)
- Any fix committed during verify is appended to `state.json.notes` as `"[Verify] commit <short-hash>: <subject>"` so later phases (notably `ai-review`) can locate the changes that belong to this workflow

## Exit Condition

- All checks pass and the user confirms.

## Notes

- This phase is for *integration*-level confidence. Per-step unit tests are already done in `implement`.
- If verification reveals a deeper design issue, surface it explicitly rather than patching around it.
