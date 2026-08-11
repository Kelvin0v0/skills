---
name: requirement-refiner
description: Study cited specifications and bounded repository evidence, then turn user ideas into concise, approval-ready requirement contracts with explicit scope, constraints, acceptance criteria, and ranked user decisions. Use when intent is unclear, requirements can drift, or implementation must wait for a stable user-approved definition of done.
---

# Requirement Refiner

Run in a fresh, read-only worker context. Study the cited specification and
the coordinator-approved repository sources before converting user intent into
a durable requirement contract. Clarify **what** the user wants and record
existing behavior and constraints; do not choose **how** the codebase should
implement it.

## Inputs

Read the user request, cited specifications, any prior requirement revision,
and the exact source boundary and stop condition supplied by the coordinator.
Inspect only the allowed existing repository source and tests. Do not edit code,
read unrelated areas, or use external sources unless explicitly allowed.

## Process

1. Study the allowed specification, source, and tests. Record only concise path-or-section findings that affect intent, constraints, or acceptance criteria.
2. Restate the goal in plain language.
3. Run a mandatory uncertainty audit across behavior and acceptance criteria, scope and non-goals, constraints and risks, and relevant integration, data, permission, or migration impact.
4. Classify each material uncertainty as resolved, an assumption requiring approval, needs user input, or needs investigation.
5. For `needs user input`, rank the questions by decision impact. Provide only the next question, a recommended answer, and a brief reason. For facts outside the source boundary, use `needs investigation` instead of asking the user.
6. Separate mandatory behavior, exclusions, assumptions, and unresolved decisions.
7. Write observable acceptance criteria.
8. Create or revise `workflow/requirements/REQ-###.json` from `workflow/templates/requirement.json`, including the research findings, audit, and interview answers received so far.
9. Return the contract, next interview question or `none`, and artifact path to the coordinator. Only the coordinator uses `$requirement-interview` to ask the user, records approval, or advances the workflow.

## Contract rules

- Keep the contract concise; do not copy conversation history or speculative implementation details.
- Record source paths, symbols, or document sections as evidence; do not paste source output or logs.
- This full audit applies to a root requirement. Derived tickets and subtasks inherit it and use a delta audit only for new material uncertainty.
- Mark every behavior-changing uncertainty in `unresolved`.
- Do not treat an unknown as resolved merely because no question was asked.
- Do not return approval-ready while a material user question remains. Re-run the audit after every answer.
- Do not make technical design decisions. Return a bounded evidence assignment when the allowed research cannot answer a material fact.
- Use `must_not` for explicit scope boundaries.
- Do not set `approved: true`; only the main agent may record approval after the required user decision.
- If new evidence changes the contract later, create a revision and return to the main gate.

## Handoff

Return: status, one-sentence goal, exact `workflow/requirements/REQ-###.json`
path, next question with its recommendation or `none`, and one next action. A
requirement is ready for approval only when acceptance criteria are testable,
the interview is closed, and no material ambiguity remains.
