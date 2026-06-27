---
name: tickets-review
description: Trace PRD requirements against generated implementation tickets. Use when the user asks to review, validate, audit, or QA tickets/issues against a PRD.
disable-model-invocation: true
---

# Tickets Review

Run a traceability audit: compare one PRD to a set of implementation tickets and report only actionable problems.

The tickets pass when every PRD requirement is meaningfully covered, no ticket contradicts the PRD, no out-of-scope work is included, and every required test or verification decision is represented.

## Inputs

Expected inputs:

- One PRD, usually Markdown
- Multiple tickets or issues, usually Markdown

If either input is missing and cannot be found from the user's message or workspace context, ask for the missing file path before reviewing.

## Process

1. Identify the exact PRD file and all ticket files.

   Completion criterion: the review has one PRD source and a complete ticket set, or a blocking question has been asked.

2. Build an internal traceability ledger from the PRD.

   Create one row for each distinct requirement, decision, constraint, or non-goal from:

   - Problem Statement
   - Solution
   - User Stories
   - Implementation Decisions
   - Testing Decisions
   - Out of Scope
   - Further Notes or terminology guidance

   Each ledger row must include:

   - Category
   - Requirement summary
   - PRD evidence
   - Whether it is positive scope, testing scope, or out-of-scope

   Completion criterion: every user story, implementation decision, testing decision, out-of-scope item, and material note from the PRD has a ledger row.

3. Trace the ticket set against the ledger.

   For each ledger row, inspect all relevant ticket sections, especially:

   - `What to build`
   - `Acceptance criteria`
   - `Blocked by`

   Treat `Blocked by` as dependency information, not implementation coverage.

   Classify each PRD row as one of:

   - Covered
   - Partially covered
   - Missing
   - Contradicted
   - Too vague to be safely implemented
   - Violated by out-of-scope ticket work

   Also scan every ticket for extra scope that does not come from the PRD.

   Completion criterion: every PRD ledger row has a classification and ticket evidence, and every ticket has been checked for contradictions or out-of-scope additions.

4. Report only actionable problems.

   Do not output the ledger unless the user asks for it.

   A finding is actionable when the user could accept or reject a specific ticket change.

## Coverage Rules

Judge the ticket set collectively. A PRD requirement may be represented by one ticket, multiple tickets, or a combination of ticket sections.

Do not require identical wording when the meaning is preserved.

A requirement is covered only when the ticket set preserves the PRD's intent, scope, terminology, and concrete details closely enough that an implementer would know what to build.

Flag a problem when:

- A PRD requirement is missing from all tickets.
- A ticket covers only part of a requirement.
- A ticket changes the PRD's intended behavior, scope, priority, terminology, or technical approach.
- A precise PRD decision is generalized into vague wording.
- A concrete value, name, limit, state, field, validation rule, default, permission rule, error behavior, rollout decision, or cache behavior is omitted or changed.
- A user story is represented only by implementation mechanics while the user-visible behavior is lost.
- A testing decision is absent, too generic, assigned to the wrong behavior, or missing required commands or review steps.
- A ticket includes work the PRD marks out of scope.
- A ticket introduces setup work, libraries, architecture, or workflows that the PRD excludes.

Do not flag a problem when:

- The requirement is clearly covered in either `What to build` or `Acceptance criteria`.
- The wording differs but the meaning and implementation contract are preserved.
- Out-of-scope behavior is mentioned only as a non-goal or guardrail.
- The problem statement or solution is distributed across multiple tickets without losing intent.

## Proposed Fix Rules

Prefer the smallest ticket change that restores traceability.

When proposing a fix:

- Name the affected ticket or tickets.
- Say whether the fix is an edit to an existing ticket or a new ticket.
- Preserve the PRD's exact numbers, names, states, and constraints.
- If one PRD requirement affects multiple tickets, propose edits to every affected ticket.
- Group duplicate coverage problems into one finding when they share the same fix.
- Split unrelated problems into separate findings.

## Output

Return a numbered list of problems.

Use this format for each problem:

```md
1. **[Short problem title]**

   **Problem:** Describe the mismatch, omission, vague requirement, or out-of-scope inclusion.

   **PRD evidence:** Quote or summarize the relevant PRD requirement.

   **Ticket evidence:** Identify the relevant ticket or tickets, or say "No ticket found."

   **Proposed solution:** Describe the specific ticket change the user can accept or reject.