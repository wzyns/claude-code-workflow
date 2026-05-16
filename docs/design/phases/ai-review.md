# AI Review Phase

Skill: `/ccw:ai-review`

Run an automatic self-review of the commits made during this workflow and emit the findings for `user-review` to act on.

## Input

- The implementation, committed to the feature branch. Uncommitted changes are invisible to the self-review since the review subject is the diffs of the recorded commits.
- `state.json.notes` entries of the form `"[<Phase>] commit <short-hash>: <subject>"` recorded by earlier phases.

## Behavior

1. Read the workflow's recorded commits from `state.json.notes` (the lines matching `"[<Phase>] commit <hash>: <subject>"`). The union of those commits' diffs is the review subject. Snapshot this list at the start of the run.
2. If the snapshot is empty, record `"AI review: no commits to review"` in `state.json.notes` and complete the phase. No findings are produced.
3. Otherwise, self-review each commit's diff and produce findings. Group by severity.
4. Write the findings list to `state.json.artifacts.ai_review_findings` using the schema described in [../05-state.md](../05-state.md). Each finding has `id`, `severity`, `summary`, and `detail`.
5. Print a short summary to the user (count by severity, headline of each finding) for visibility, but do not ask any questions.

## Output

- `state.json.artifacts.ai_review_findings` populated (or explicitly empty, with a note in `state.json.notes` explaining why).
- No code changes. No commits. Per-finding decisions (reflect / skip / defer) happen in `user-review`.

## Exit Condition

- Findings have been written (or the empty-snapshot case has been recorded). The phase then completes automatically.

## Notes

- AI review feedback is advisory. The `user-review` phase is responsible for deciding which findings to act on.
- This phase does not prompt for "proceed or skip" — it always runs. To skip the review entirely, the user can interrupt the orchestrator and request `back` or stop.
