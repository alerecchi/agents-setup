---
name: staff-code-review
description: Audit code changes, especially AI-generated code, for correctness, security, production risk, architectural fit, unnecessary code, duplicated behavior, and test quality. Use when the user asks to review code, audit a change, inspect uncommitted work, evaluate tests, or check implementation quality.
disable-model-invocation: true
---

# Staff Code Review

Run a staff-level, skeptical audit of the requested change. If the user does not name a branch, commit, PR, or patch, review the current uncommitted work, including staged and unstaged changes.

The review is complete only when every changed file, every changed hunk, and every changed line has been considered.

## Workflow

1. Identify the review target.
   - If the user names a change, review that change.
   - Otherwise, review uncommitted code.

2. Read project guidance before judging the change.
   - Read `AGENTS.md` if present.
   - Read `CONTEXT.md` if present.
   - Read relevant ADRs under `docs/adr/` if present.
   - Treat documented project standards as review criteria.

3. Inspect the full diff.
   - Review all changed files before writing the final answer.
   - Include staged and unstaged changes for uncommitted reviews.
   - Read nearby unchanged code when needed to understand the behavior.
   - Follow call sites, imports, types, tests, and configuration when they affect the change.

4. Run the line audit.
   For every changed line, ask:
   - Does this line need to exist?
   - Is it solving a real requirement?
   - Is the same behavior already implemented elsewhere in the changed code or nearby code?
   - Is this abstraction earning its cost?
   - Is this branch, option, type, mock, helper, or config now unused or redundant?
   - Could this line create a correctness, security, reliability, or maintenance risk?

   If a line looks unnecessary, duplicated, speculative, or over-engineered, either report it or explicitly decide why it is harmless enough to leave alone.

5. Inspect tests.
   - Identify which behavior the tests prove.
   - Check whether the tests would fail if the important behavior were broken.
   - Look for missing edge cases, excessive mocking, implementation-detail assertions,
     flaky tests, weak snapshots, and missing regression coverage.
   - Treat risky behavior without meaningful tests as a finding.

6. Classify issues.
   - `Blocking finding`: must be fixed before the change is safe.
   - `Non-blocking suggestion`: useful improvement, not required for safety.
   - `Needs user review`: suspicious or ambiguous code where the reviewer cannot prove
     it is wrong, but the user should consciously decide.

7. Write only defensible findings.
   - Do not invent issues.
   - Do not nitpick harmless style unless it violates documented standards.
   - Prefer fewer, stronger findings.
   - Every finding must name the exact file and line.
   - Every finding must explain the issue, the impact, and a concrete fix.

## Review Criteria

### Correctness

Look for broken business logic, invalid assumptions, incorrect edge cases, bad state
transitions, race conditions, data loss, API contract breaks, backwards compatibility
breaks, and error handling gaps.

### Security And Production Risk

Look for injection, auth bypasses, sensitive data exposure, unsafe parsing or
deserialization, insecure defaults, missing validation, unsafe filesystem/network/shell
operations, secret or personal data logging, abuse risks, observability gaps, rollback
risks, and reliability hazards.

### Architecture And Maintainability

Look for conflicts with `AGENTS.md`, `CONTEXT.md`, or ADRs; duplicated logic;
unnecessary abstractions; speculative options; leaky boundaries; tight coupling that
creates real risk; inconsistent domain language; and code that does not match existing
project patterns.

AI-generated code deserves extra deletion pressure: helpers, wrappers, types, comments,
tests, mocks, fallbacks, config, and branches must each earn their place.

### Tests

Tests should prove behavior, not implementation trivia. Flag tests that would pass
despite the bug, tests that mock the unit under test, broad mocks that hide integration
failures, missing edge cases, nondeterminism, and snapshots without meaningful
assertions.

## Output Format

Lead with findings. Group them in this order:

## Blocking Findings

### Short finding title

**Where:** `path/to/file.ext:line`

**Issue:**  
Explain exactly what is wrong.

**Why it matters:**  
Explain the concrete risk or impact.

**How to fix:**  
Explain the fix. Include corrected code only when it makes the fix clearer.

## Non-Blocking Suggestions

Use the same format as blocking findings.

## Needs User Review

Use this section for suspicious code that may be intentional but deserves a conscious
decision. Be clear about what evidence is missing.

## No Findings

If there are no blocking findings, no non-blocking suggestions, and no user-review
items, write:

No findings.

I reviewed the target change for correctness, security risk, production risk,
architecture alignment, test quality, project standards, duplicated behavior, and
unnecessary code. I did not find anything that needs to be addressed.
