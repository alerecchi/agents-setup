---
name: staff-code-review
description: Performs rigorous code reviews focused on logic errors, security risks, production risks, test quality, architectural alignment, and project standards compliance. Use when the user asks to review code, review a change, inspect uncommitted work, audit implementation quality, or evaluate tests.
---

# Code Review

## Quick start

Review the requested code change. If the user does not specify a change id, review the
current uncommitted code.

Prioritize findings that are real, actionable, and important. Focus on:

- Logic errors
- Security vulnerabilities
- Production risks
- Architectural misalignments
- Test quality and meaningful edge-case coverage
- Incorrect or excessive mocking
- Compliance with project standards in:
  - `AGENTS.md`
  - `CONTEXT.md`
  - `docs/adr/`

Also verify the implementation is not over-engineered. Keep only valid abstractions;
call out unnecessary complexity that should be simplified.

## Review workflow

1. Identify the review target:
   - If the user provides a change id, review that change.
   - Otherwise, review uncommitted code.
2. Inspect the project guidance:
   - Read `AGENTS.md` if present.
   - Read `CONTEXT.md` if present.
   - Read relevant files under `docs/adr/` if present.
3. Inspect all changed files before responding.
   - Do not produce the final review after inspecting only a subset of changed files.
   - Review both staged and unstaged changes when reviewing uncommitted code.
   - Include related nearby context when needed to understand behavior.
4. Inspect related tests.
5. Identify only real findings.
6. Classify each item as either:
   - `Blocking finding` — must be fixed before the change is considered safe.
   - `Non-blocking suggestion` — optional improvement that is valid but not required
     for safety or correctness.
7. For every finding, assume full responsibility:
   - Be specific.
   - Explain the issue.
   - Explain why it matters.
   - Explain how to fix it.
8. If a fix requires code, include the corrected code in a markdown code block inside
   the `How to fix` section.
9. If no findings are found, output a celebratory verdict.

## Review focus

### Correctness

Look for:

- Broken business logic
- Incorrect edge-case handling
- Race conditions
- Invalid assumptions
- Error handling gaps
- Data loss or data corruption risks
- Incorrect state transitions
- Incorrect API contracts
- Backward compatibility issues

### Security and production risk

Look for:

- Injection vulnerabilities
- Authentication or authorization bypasses
- Sensitive data exposure
- Unsafe deserialization or parsing
- Insecure defaults
- Missing validation
- Insufficient rate limiting or abuse protection
- Unsafe filesystem, network, or shell operations
- Logging of secrets or personal data
- Reliability, observability, rollback, and operational risks

### Architecture and maintainability

Look for:

- Misalignment with `AGENTS.md`, `CONTEXT.md`, or ADR decisions
- Unnecessary abstractions
- Over-engineered designs
- Duplicated logic that should be consolidated
- Tight coupling where it creates real maintenance risk
- Leaky abstractions
- Inconsistent naming or domain modeling
- Violations of established project patterns

Do not flag style preferences unless they create concrete risk or violate documented
project standards.

### Tests

Ensure tests are meaningful. Look for:

- Missing coverage for important behavior
- Missing edge cases
- Tests that assert implementation details instead of behavior
- Overly broad mocks that hide real failures
- Inappropriate mocking of the unit under test
- Flaky or nondeterministic tests
- Tests that do not fail on the bug they claim to cover
- Snapshot tests without meaningful assertions
- Missing regression tests for risky fixes

## Blocking findings vs non-blocking suggestions

Use `Blocking finding` for issues that affect:

- Correctness
- Security
- Data integrity
- Production reliability
- Compliance with documented project standards
- Architectural decisions recorded in ADRs
- Meaningful test coverage for risky behavior
- Over-engineering that creates concrete maintenance or correctness risk

Use `Non-blocking suggestion` for improvements that are helpful but not required, such
as:

- Minor simplifications
- Naming improvements
- Additional low-risk test cases
- Small readability improvements
- Optional refactors that do not affect safety or correctness

Do not mix blocking findings and non-blocking suggestions together. Keep them in
separate sections.

## Output format

1. Short finding title

**Issue:**  
Explain exactly what is wrong.

**Why it matters:**  
Explain the concrete risk or impact.

**How to fix:**  
Explain the fix. If code is needed, include it here:

```language
// corrected code
```

Where:
path/to/file.ext:line

If there are no findings, use this format:
🎉 Excellent work -no findings.

I reviewed the target code for logic errors, security risk, production risk,
architecture alignment, test quality, and project standards compliance. I did not
find any issues that need to be addressed.

## Review discipline

- Do not invent findings.
- Do not nitpick harmless style differences.
- Do not defer responsibility with vague language.
- Do not say something "might be wrong" unless explaining the exact condition.
- Prefer fewer, stronger findings over many speculative comments.
- Every finding must be actionable.
