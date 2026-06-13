---
name: tickets-review
description: Reviews and validates implementation tickets against a PRD to find missing, incorrect, vague, or out-of-scope work. Use when the user asks to review, validate, compare, audit, or QA tickets/issues created from a PRD, especially with PRD markdown files and ticket markdown files.
---

# Tickets Review

## Quick start

Review a PRD and a set of implementation tickets to validate that the tickets, taken together, accurately and completely represent the PRD.

Expected inputs:

- One PRD, usually a Markdown file
- Multiple tickets or issues, usually Markdown files

Common PRD sections include:

- Problem Statement
- Solution
- User Stories
- Implementation Decisions
- Testing Decisions
- Out of Scope
- Further Notes

Common ticket sections include:

- What to build
- Acceptance criteria
- Blocked by

Output only a numbered list of problems. Each problem must include:

- The issue found
- Evidence from the PRD
- Evidence from the ticket set, or note that no ticket covers it
- A proposed ticket change for the user to accept or reject

If there are no problems, state that no problems were found.

## Review workflow

1. Identify the source files:
   - Locate the PRD file.
   - Locate all ticket or issue files.
   - If either is missing, ask the user to provide it.

2. Read the PRD and extract the canonical requirements:
   - Overall problem being solved
   - Intended solution
   - Further notes and terminology guidance
   - Every user story
   - Every implementation decision
   - Every testing decision
   - Every out-of-scope item

3. Read all tickets and build a collective coverage map:
   - Treat the full ticket set as the implementation plan.
   - A PRD requirement may be represented in one ticket, multiple tickets, or a combination of ticket sections.
   - Check `What to build` and `Acceptance criteria` carefully, because requirements may appear in either place.
   - Do not expect any single ticket to contain the full PRD problem statement, full solution, or all user stories.
   - Validate whether the tickets collectively preserve the PRD's intent, scope, terminology, and detailed decisions.
   - When a PRD requirement affects a shared area, reusable pattern, cross-cutting behavior, or component used by multiple tickets, identify all tickets whose work would be affected by that requirement.

4. Validate coverage and correctness:
   - Confirm the PRD's Problem Statement, Solution, and Further Notes are accurately represented by the tickets as a whole.
   - Confirm every user story has at least one meaningful representation in the ticket set.
   - Confirm every implementation decision is reflected precisely in the ticket set.
   - Confirm out-of-scope work does not appear in any ticket as work to implement.
   - Confirm every testing decision has at least one meaningful representation in the ticket set.

5. Report only actionable problems:
   - Do not list items that are already adequately covered.
   - Do not require identical wording when the meaning is preserved.
   - Do not rewrite all tickets unless asked.
   - Prefer specific edits to specific tickets.
   - If the best fix is a new ticket, say so clearly.
   - Group duplicate issues into one problem when they share the same proposed fix.
   - Separate unrelated issues into separate numbered problems.
   - When proposing a fix for a requirement that affects multiple tickets, propose edits to all affected tickets.

## Validation rules

### Problem Statement, Solution, and Further Notes

Check whether the ticket set collectively preserves the intent of the PRD.

Flag problems when:

- The tickets solve a materially different problem.
- The core solution is absent or contradicted.
- Important notes, terminology, or domain-language guidance are missing from the tickets.
- Ticket wording changes the PRD's scope, priority, or intended behavior.
- A ticket introduces ambiguity where the PRD is specific.

Do not flag a problem merely because the problem statement or solution is split across multiple tickets.

### User Stories

Every user story in the PRD must have at least one meaningful representation in the tickets.

A user story is represented when one or more tickets cover:

- The intended action, behavior, or capability
- The expected outcome, value, or constraint

Flag problems when:

- A user story is missing entirely.
- Only part of the user story is represented.
- The represented behavior is materially different from the PRD.
- A user story is split across tickets in a way that loses required context.
- Acceptance criteria cover implementation mechanics but omit the user-visible behavior required by the story.

### Implementation Decisions

Every implementation decision in the PRD must be reflected in the tickets when it is relevant to the ticketed scope.

Treat concrete details as required, including:

- Exact numbers and limits
- Required names and terminology
- Component names
- Data fields
- Required states and transitions
- Validation rules
- UI behavior
- API or server-function behavior
- Storage or persistence decisions
- Ownership, authorization, or privacy rules
- Cache update behavior
- Error handling
- Defaults
- Feature flags
- Migration or rollout decisions
- Testing or command requirements that are part of implementation completion

Flag problems when:

- A ticket mentions a feature but omits a required implementation detail.
- A ticket uses a different number, name, state, rule, or behavior than the PRD.
- A ticket generalizes a precise PRD decision into ambiguous wording.
- A decision is only implied but not explicitly actionable.
- A ticket exposes or allows something the PRD says should not be exposed.
- A ticket includes a conflicting technical approach.

Example:

- PRD says: "Use a shared curated palette of 20 color presets."
- Ticket says: "Add color selection for tags and categories."
- Problem: the ticket omits the shared curated palette and the exact count of 20 presets.

### Out of Scope

Out-of-scope PRD items must not appear in tickets as work to implement.

Flag problems when:

- A ticket includes an out-of-scope feature as a requirement.
- A ticket includes setup work for an explicitly excluded capability.
- A ticket treats a future enhancement as part of the current implementation.
- Acceptance criteria require out-of-scope behavior.
- A ticket adds a new library, architecture, or workflow that the PRD excludes.

Do not flag out-of-scope items when:

- A ticket explicitly says the item is excluded.
- A ticket adds guardrails to prevent the out-of-scope behavior.
- A ticket documents non-goals without requiring implementation.

### Testing Decisions

Every testing decision in the PRD must have at least one meaningful representation in the tickets.

Testing decisions may include:

- Server-function tests
- Dialog behavior tests
- Card behavior tests
- Cache behavior checks
- Integration-level assertions
- Manual verification steps
- Format, test, review, or agent commands
- Authorization and ownership boundary tests
- Normalization tests
- Relationship semantics tests
- Error handling tests

Flag problems when:

- A testing requirement is absent from all tickets.
- A ticket references testing generally but omits a specific required test type or scenario.
- A testing decision is assigned to the wrong behavior or incomplete scope.
- Required verification commands are missing.
- Required review steps are missing.

## Ticket evidence guidelines

When citing ticket evidence, inspect both the narrative and checklist sections.

For tickets with sections like `What to build`, `Acceptance criteria`, and `Blocked by`:

- Treat `What to build` as the ticket's implementation intent.
- Treat `Acceptance criteria` as the concrete completion contract.
- Treat `Blocked by` as dependency information, not implementation coverage unless it explicitly describes required work.

A requirement can be considered covered if it appears clearly in either `What to build` or `Acceptance criteria`.

A requirement should be flagged if it is only weakly implied and would be easy for an implementer to miss.

When proposing ticket changes:

- Match the proposed fix to the scope of the missing or incorrect requirement.
- If the requirement affects only one ticket, propose a change to that ticket.
- If the requirement affects multiple tickets, name the affected tickets and propose the needed edits across them.

## Output format

Return a numbered list of problems.

Use this format for each item:

```md
1. **[Short problem title]**

   **Problem:** Describe the mismatch, omission, vague requirement, or out-of-scope inclusion.

   **PRD evidence:** Quote or summarize the relevant PRD requirement.

   **Ticket evidence:** Identify the relevant ticket or tickets, or say "No ticket found."

   **Proposed solution:** Describe the specific ticket change the user can accept or reject.
```

If no problems are found, respond with:

```md
No problems found. The provided tickets adequately represent the PRD requirements, user stories, implementation decisions, out-of-scope constraints, and testing decisions.

```
