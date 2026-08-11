# Workflow Factory Operating Contract

## Authority

Only the main agent may update `workflow/state/current.json`, change scope,
approve a requirement or plan, suspend or resume work, or declare completion.

## Required flow

`clarify (if needed) -> save requirement -> requirement approval -> create tickets ->
select ticket -> detailed ticket plan -> plan approval -> implement ->
independent verification -> completion`

Do not implement before the requirement and selected ticket plan are approved.
Do not treat worker self-checks as final verification.

## Subagents

Run substantial exploration, planning, implementation, investigation, and
verification in fresh, bounded subagent contexts to keep the main context small.

Give a subagent only the relevant artifact paths, explicit scope, and required
output. It returns artifact paths, concise evidence, risks or blocks, and a
recommended next action. It cannot update state, approvals, scope, or completion.

Keep user decisions, tiny work, routing, and all workflow-state changes with
the main agent.

## Durable state

`current.json` is the small active-work snapshot. Detailed requirements,
tickets, plans, reports, and queued interruptions live in their linked artifacts.

## New user input

Classify input before changing active work:

- Clarification without scope change: answer and continue.
- New requirement or unrelated bug: queue it; it must become its own approved work later.
- Active-ticket failure: verify, then repair only if the approved plan covers it.
- Safety or production blocker: suspend active work and let the main agent route recovery.

Queued items never silently modify the active requirement or ticket.

## Git safety

Preserve unrelated changes. Commit only when requested. Never force-push, reset,
or delete material without explicit authorization.
