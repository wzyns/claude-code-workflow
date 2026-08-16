---
name: ai-review
description: Fourth phase of the ccw workflow. Auto self-review the commits recorded in state.json.notes for this workflow and emit findings to state.json.artifacts.ai_review_findings. No "proceed/skip" prompt and no fix commits — per-finding decisions happen in user-review. Use this when the user invokes /ccw:ai-review directly, or when the orchestrator (/ccw:start) delegates the AI review phase.
---

# /ccw:ai-review — AI Review

Auto self-review the commits recorded for this workflow and write the findings to `state.json.artifacts.ai_review_findings`. This phase does not prompt the user, does not modify code, and does not create commits — `user-review` decides what to do with the findings.

## Inputs
- The implementation, committed to the feature branch. Uncommitted changes are invisible to the self-review since the review subject is the diffs of the recorded commits.
- `state.json.notes` entries of the form `"[<Phase>] commit <short-hash>: <subject>"` recorded by earlier phases.

## Procedure

1. **Collect the review subject.** Read `state.json.notes` and extract every line matching `"[<Phase>] commit <short-hash>: <subject>"`. The set of `<short-hash>` values is the review subject. Snapshot this list at the start of the run.
2. **Handle the empty case.** If the snapshot is empty (no commits have been recorded yet for this workflow), append a single note `"AI review: no commits to review"` to `state.json.notes`, set `state.json.artifacts.ai_review_findings = []`, and complete the phase. Stop here.
3. **Self-review.** For each commit, read its diff (`git show <hash>`) and produce findings. Findings should be grouped by severity (`important`, `nit`, `info`) with concrete code references (file:line) where useful, included in the `detail` field.
4. **Write findings.** Set `state.json.artifacts.ai_review_findings` to a JSON array of findings. Each finding follows this minimal schema:
   - `id` — short identifier within the run (e.g., `F1`, `F2`).
   - `severity` — one of `important`, `nit`, `info`.
   - `summary` — one-line headline.
   - `detail` — longer explanation, with file/line references in free-form text when useful.
5. **Print a short summary** to the user (count by severity and a one-line headline per finding) for visibility. Do not ask any questions and do not wait for confirmation.

## Output
- `state.json.artifacts.ai_review_findings` populated as a structured list (or `[]` for the empty-snapshot case).
- No code changes, no commits.

## Exit condition
Findings have been written (or the empty-snapshot note has been added). The phase completes automatically without a user prompt.

## What NOT to do
- Don't ask "proceed or skip" — the phase always runs.
- Don't apply fixes or create commits in this phase. Per-finding decisions (reflect / skip / defer) belong to `user-review`.
- Don't invoke external review tools. The review is the assistant's own pass over the recorded commits.
- Don't include commits outside the snapshotted list (e.g., commits from earlier merged work).
- Don't treat AI review feedback as mandatory; it's advisory input for `user-review`.
