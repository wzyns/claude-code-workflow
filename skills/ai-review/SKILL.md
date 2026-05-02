---
name: ai-review
description: Sixth phase of the ccw workflow. Ask the user only "proceed or skip". On proceed, self-review the commits recorded in state.json.notes for this workflow; on skip, record the reason and complete the phase. Use this when the user invokes /ccw:ai-review directly, or when the orchestrator (/ccw:start) delegates the AI review phase.
---

# /ccw:ai-review — AI Review

Self-review the commits recorded for this workflow and decide which feedback to apply.

## Inputs
- The implementation, committed to the feature branch. Uncommitted changes are invisible to the self-review since the review subject is the diffs of the recorded commits.
- `state.json.notes` entries of the form `"[<Phase>] commit <short-hash>: <subject>"` recorded by earlier phases

## Procedure

1. **Suggest committing** any uncommitted changes first, so the review covers a stable snapshot.
2. **Ask the user only "proceed or skip".**
   - On **skip**, record the reason in `state.json.notes` (e.g., `"AI review skipped: <reason>"`) and treat the phase as complete. Stop here.
   - On **proceed**, continue.
3. **Collect the review subject.** Read `state.json.notes` and extract every line matching `"[<Phase>] commit <short-hash>: <subject>"`. The set of `<short-hash>` values is the review subject. Snapshot this list at the start of the run — fix commits added later are not re-reviewed in the same run.
   - If the snapshot is empty (no commits have been recorded yet for this workflow), report this to the user and ask whether to skip the phase or wait until commits exist. Do not invent a different review subject.
4. **Self-review.** For each commit, read its diff (`git show <hash>`) and produce findings. Findings should be grouped by severity (critical, important, nit) with concrete code references (file:line) where possible.
5. **Summarize** the findings for the user.
6. **Discuss with the user** which items to address:
   - For each item, propose: "address now" / "skip" / "defer to follow-up"
   - For "skip" or "defer", record the reason in `state.json.notes`
7. **Apply changes** for items to address. After each fix:
   - Re-run `config.test_command` to make sure nothing broke
   - If the fix is committed, append a one-line record to `state.json.notes` in the format `"[AI Review] commit <short-hash>: <subject>"` (use `git log -1 --pretty='%h %s'`). Commits made during the same ai-review run are not re-reviewed in this run.
8. Summarize what was addressed and what was intentionally skipped.

## Output
- Code changes addressing review feedback
- Entries in `state.json.notes` describing items addressed and items intentionally skipped

## Exit condition
The user confirms the phase is complete (feedback addressed, or skipped).

## What NOT to do
- Don't ask the user to choose a review mechanism — only "proceed or skip".
- Don't invoke external review tools. The review is the assistant's own pass over the recorded commits.
- Don't blindly apply all review feedback. Discuss trade-offs with the user.
- Don't treat AI review feedback as mandatory; it's advisory.
- Don't include commits outside the snapshotted list (e.g., commits from earlier merged work, or fix commits this run created).
