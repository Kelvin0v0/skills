---
name: requirement-refiner
description: Turn vague user ideas into concise, approval-ready requirement contracts with explicit scope, constraints, and acceptance criteria. Use when intent is unclear, requirements can drift, or implementation must wait for a stable user-approved definition of done.
---

# Requirement Refiner

Run in a fresh worker context. Convert user intent into a durable requirement
contract. Clarify **what** the user wants; do not decide **how** the codebase
should implement it.

## Process

1. Restate the goal in plain language.
2. Run a mandatory uncertainty audit across behavior and acceptance criteria, scope and non-goals, constraints and risks, and relevant integration, data, permission, or migration impact.
3. Classify each material uncertainty as resolved, an assumption requiring approval, needs user input, or needs investigation.
4. For `needs user input`, rank the questions by decision impact. Provide only the next question, a recommended answer, and a brief reason. For discoverable facts, use `needs investigation` instead of asking the user.
5. Separate mandatory behavior, exclusions, assumptions, and unresolved decisions.
6. Write observable acceptance criteria.
7. Create or revise `workflow/requirements/REQ-###.json` from `workflow/templates/requirement.json`, including the audit and interview answers received so far.
8. Return the contract, next interview question or `none`, and artifact path to the coordinator. Only the coordinator uses `$grill-me` to ask the user, records approval, or advances the workflow.

## Contract rules

- Keep the contract concise; do not copy conversation history or speculative implementation details.
- This full audit applies to a root requirement. Derived tickets and subtasks inherit it and use a delta audit only for new material uncertainty.
- Mark every behavior-changing uncertainty in `unresolved`.
- Do not treat an unknown as resolved merely because no question was asked.
- Do not return approval-ready while a material user question remains. Re-run the audit after every answer.
- Use `must_not` for explicit scope boundaries.
- Do not set `approved: true`; only the main agent may record approval after the required user decision.
- If new evidence changes the contract later, create a revision and return to the main gate.

## Handoff

Return: status, one-sentence goal, exact `workflow/requirements/REQ-###.json`
path, next question with its recommendation or `none`, and one next action. A
requirement is ready for approval only when acceptance criteria are testable,
the interview is closed, and no material ambiguity remains.
