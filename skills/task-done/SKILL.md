---
name: task-done
description: Verifies that code work is complete by running required checks, review, and completion steps. Use after TDD implementation, after implementing a ticket, before declaring a coding task complete, or when the user asks to ensure work is done.
---

# Task Done

## Purpose

Use this skill after implementing a ticket or a new implementation request from the user and before saying implementation work is complete.

A task is not done until required checks pass, reviews are handled, and any ticket status update is completed.

## Checklist

1. Run required checks from the project root: `pnpm run build`, `pnpm run format` and `pnpm test`. All checks must pass.
2. Run a Staff Code Reviewer sub-agent. If the user did not explicitly authorize sub-agents, ask for permission. Ask the sub agent to review the uncommitted changes and address all valid comments it produces. If a comment is not worth addressing, explain why. If you cannot spawn the sub agent explain why.
3. Run CodeRabbit on the uncommitted code. Use your CodeRabbit skill and run required commands outside the sandbox. Address all valid comments it produces. If a comment is not worth addressing, explain why. If CodeRabbit didn't work explain why.

## Follow-up changes

If the user asks for follow-up changes after an implementation, ask before running the full checklist:
`Do you want me to run the task-done checklist again for these follow-up changes?`

## Task Done report

```md
- Build: passed / failed / skipped
- Format: passed / failed / skipped
- Tests: passed / failed / skipped
- Staff Code Reviewer: completed / unavailable / skipped
- CodeRabbit: completed / failed / skipped / not required
- Ticket status updated: yes / no / n/a

Summary:
[Final state and any important notes]
Notes:
[Any skipped checks, unresolved comments, or failures]
```

Only say the task is complete if all required checks and reviews passed, or if skipped steps were explicitly waived by the user.
