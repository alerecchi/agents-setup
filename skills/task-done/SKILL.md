---
name: task-done
description: Done gate for code work. Use after TDD implementation, after implementing a ticket, before declaring implementation complete, after follow-up code changes unless the user waives the full gate, or when the user asks to verify that work is done.
---

# Task Done

## Rule

This skill is the done gate for implementation work.

Only say the task is complete when required checks pass, required reviews have been handled, and any requested ticket/status update is done. If a step cannot run, report that the task is not fully verified unless the user explicitly waives that step.

This skill verifies work and reports the result. When a required check or valid review finding fails, fix it only if the user has asked you to complete the implementation work. If the user asked only for verification, report the failure and stop.

## Steps

1. Establish the scope.

   Inspect the current git state and identify the changed files, project root, stack, and available check commands. If the chat includes a ticket file, issue, PRD, acceptance criteria, or implementation brief, keep that as the review target.

   Completion criterion: you can state what changed, what standard the work must satisfy, and which checks/reviews will run.

2. Run required local checks.

   For a React app, run checks from the project root with `pnpm`: `pnpm run format`, `pnpm run build` and `pnpm test`. 

   Completion criterion: every required local check has passed, failed with output summarized, or been skipped with a concrete reason.

3. Run Staff Code Reviewer.

   Ask for permission to run a Staff Code Reviewer subagent unless the user has already explicitly authorized subagents. The subagent should review the uncommitted changes. When a ticket file or brief is available in the chat, run the subagent without extra conversation context and provide only that ticket or brief as input.

   Ask the subagent to review correctness, missing tests, regressions, security risk, and maintainability. Address all valid comments when you are completing implementation work. If a comment is not worth addressing, explain why. If you cannot spawn the subagent, explain why.

   Completion criterion: every Staff Code Reviewer comment is fixed, rejected with rationale, or reported as unresolved.

4. Run CodeRabbit.

   Use the CodeRabbit skill on the uncommitted code, and run required CodeRabbit commands outside the sandbox. Address all valid comments when you are completing implementation work. If a comment is not worth addressing, explain why. If CodeRabbit does not work, explain why.

   Completion criterion: every CodeRabbit comment is fixed, rejected with rationale, or reported as unresolved.

5. Re-check after fixes.

   If you changed files while handling failed checks or review comments, rerun the local checks affected by those changes. If the impact is broad or uncertain, rerun the Staff Code Reviewer and CodeRabbit again.

   Completion criterion: the final working tree state, not an earlier state, has been checked or the missing re-check is reported.

6. Update ticket/status if applicable.

   If a ticket, issue, PR, checklist is available in the chat, set the status to `done`, do it after the done gate is green.

   Completion criterion: requested status updates are done, or the report explains why they were not.

## Follow-up Changes

If the user asks for a small follow-up change after a completed implementation, do the requested change and run only the checks that directly cover that change.

Report that Staff Code Reviewer and CodeRabbit were skipped because the follow-up is small.

## Task Done Report

```md
- Build: passed / failed / skipped / n/a
- Format/Lint: passed / failed / skipped / n/a
- Tests: passed / failed / skipped / n/a
- Staff Code Reviewer: completed / unavailable / skipped
- CodeRabbit: completed / failed / skipped / n/a
- Re-check after fixes: passed / failed / skipped / n/a
- Ticket/status update: yes / no / n/a

Summary:
[Final state and any important notes]

Notes:
[Skipped checks, unresolved comments, rejected comments, failures, or user waivers]
```

Only say the task is complete if all required checks and reviews passed, or if skipped steps were explicitly waived by the user.
