# Workflow Factory Operating Contract

## Authority

Only the main agent may update `workflow/state/current.json`, change scope,
approve a requirement or plan, suspend or resume work, or declare completion.

## Required flow

`mandatory uncertainty audit -> save requirement -> requirement approval -> create tickets ->
select ticket -> detailed ticket plan -> plan approval -> implement ->
independent verification -> completion`

Do not implement before the requirement and selected ticket plan are approved.
Do not treat worker self-checks as final verification.

## Mandatory uncertainty audit

Before saving a requirement, classify each material uncertainty as resolved,
an assumption requiring approval, needs user input, or needs investigation.
Check behavior and acceptance criteria, scope and non-goals, constraints and
risks, and any relevant integration, data, permission, or migration impact.
Do not advance while a material uncertainty needs user input or investigation;
an unknown is not resolved merely because no question was asked.

## Delivery profile

At requirement approval, the main agent sets `delivery_profile` to `light`,
`standard`, or `complex`. It may revise the profile only at a gate when scope
or risk changes. Profiles control artifact detail and delegation, not authority
or the need for appropriate verification.

## Implementation boundary

An approved ticket plan defines its material behavior, reuse/design decision,
boundaries, and checks. Workers may choose normal local names and private
helpers. Return to planning only when implementation would materially change
behavior, reuse/design, API/data/permissions/migration, scope, risk, or boundaries.

## Subagents

Delegate in a fresh, bounded subagent context only when work needs independent
evidence, is safely independent, or would distract the main context.

Give a subagent only the relevant artifact paths, explicit scope, and required
output. It returns artifact paths, concise evidence, risks or blocks, and a
recommended next action. It cannot update state, approvals, scope, or completion.

Keep user decisions, tiny work, routing, and all workflow-state changes with
the main agent.

## Durable state

`current.json` is the small active-work snapshot, not a chat cache. Update it
only at approvals, ticket selection, investigation conclusions, verification,
pause/resume, and completion. Detailed requirements, tickets, plans, reports,
and queued interruptions live in their linked artifacts.

## New user input

Classify input before changing active work:

- Clarification without scope change: answer and continue.
- New requirement or unrelated bug: queue it; it must become its own approved work later.
- Active-ticket failure: verify, investigate, repair only if the plan covers it,
  then verify again in a fresh context; otherwise return to requirement approval.
- Safety or production blocker: suspend active work and let the main agent route recovery.

Queued items never silently modify the active requirement or ticket.

## Git safety

Preserve unrelated changes. Commit only when requested. Never force-push, reset,
or delete material without explicit authorization.
