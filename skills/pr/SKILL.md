---
name: pr
description: Eighth and final phase of the ccw workflow. Create the pull request following Claude Code's PR protocol, then add development notes (trade-offs, known limitations, follow-ups) as PR comments. Sets state.json.current_phase to done. Use this when the user invokes /ccw:pr directly, or when the orchestrator (/ccw:start) delegates the PR phase.
---

# /ccw:pr — Pull Request Creation

Create the pull request and capture the workflow's accumulated context as PR comments.

## Inputs
- Final code on the feature branch
- `state.json.notes` (accumulated through prior phases)
- `state.json.config.branch_name`

## Procedure

1. **Follow the standard Claude Code PR protocol**:
   a. Run `git status`, `git diff`, `git log` to understand the full set of changes.
   b. Analyze **all** changes since the branch diverged from the base — not just the latest commit.
   c. Draft a PR title (under 70 chars) and body with:
      - **Summary**: 1–3 bullets, what changed and why
      - **Test plan**: markdown checklist
   d. Show the title and body to the user for approval.
2. **On user approval**:
   a. Push the branch (`git push -u origin <branch-name>` if it isn't already tracking a remote).
   b. Run `gh pr create` with the approved title and body. Use HEREDOC for the body to preserve formatting.
3. **Add development notes as PR comments**. Pull from `state.json.notes` and prior phases. Categories to include if applicable:
   - Trade-offs made during design or implementation
   - Known limitations
   - Follow-up tasks deferred from this PR
   - Anything reviewers should pay extra attention to
   - Items skipped from AI review (with reasons)

   Ask the user whether to post these as **separate comments per category** or a **single consolidated comment with sections**. Then post via `gh pr comment`.
4. **Update state.json**:
   - Push `pr` into `completed_phases`
   - Set `current_phase = "done"`
   - Set `artifacts.pr_url` to the created PR URL
   - Update `updated_at`
5. **Report the PR URL** to the user as the final output.

## Output
- A live PR URL
- PR comments documenting development notes
- `state.json.current_phase = "done"`

## Exit condition
PR is created and the workflow is marked complete.

## What NOT to do
- Don't push without showing the title/body to the user first.
- Don't use `--no-verify`, `--no-gpg-sign`, or `--force` flags. If hooks fail, fix the underlying issue and create a new commit.
- Don't create the PR while there are uncommitted changes — commit first or ask the user.
- Don't skip the PR-comments step — those notes are why the workflow accumulated context in the first place.
- Don't push to `main`/`master` directly. Always use the feature branch.
- Don't squash the per-step commits from `/ccw:implement` by default — preserve them as separate commits so reviewers can step through the implementation history.
