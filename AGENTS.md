# Workflow Factory Operating Contract

## Authority

The coordinator is the main agent. Only the coordinator may update `workflow/state/current.json`, change scope,
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

The first or root requirement always receives this full audit. A ticket or
subtask derived from an approved requirement inherits it and runs a delta audit
only when planning or investigation exposes new material uncertainty.

## Mandatory requirement interview

For every root requirement, the audit enters a mandatory clarification loop
before tickets or planning. A fresh, read-only requirement-refiner first studies
the cited specification and the coordinator-approved repository source and
tests, then writes the draft, concise findings, audit, and ranked material user
questions. For each `needs user input`, the coordinator uses `$requirement-interview`: ask
exactly one decision question, include a recommended answer and brief reason,
wait for the answer, record it in the requirement artifact, then re-run the
audit in a fresh refiner context.

Do not ask the user for discoverable facts. The refiner may gather only its
approved read-only intake evidence; if the question needs a broader repository
or external search, assign a separate bounded evidence worker. The loop closes
only when no material question remains, every assumption is explicitly
approved, and every assigned investigation has returned a conclusion. If the
root audit finds no user question, record that result and continue; the
mandatory stage is the audit and interview protocol, not needless questioning.

## Coordinator evidence boundary

The coordinator may read user input and workflow artifacts only. It must not
search product source, run tests, reproduce defects, browse external sources,
or make technical design decisions. When material evidence is needed, assign a
read-only evidence worker with its question, evidence level (`requirement`,
`repository`, `external`, or a combination), allowed sources, stop condition,
and required report. Prefer approved artifacts, then repository evidence, then
official external sources; use external sources only when earlier evidence is insufficient.

## Delivery profile

At requirement approval, the coordinator sets `delivery_profile` to `light`,
`standard`, or `complex`. It may revise the profile only at a gate when scope
or risk changes. Profiles control artifact detail and delegation, not authority
or the need for appropriate verification.

## Implementation boundary

An approved ticket plan defines its material behavior, reuse/design decision,
boundaries, and checks. Workers may choose normal local names and private
helpers. Return to planning only when implementation would materially change
behavior, reuse/design, API/data/permissions/migration, scope, risk, or boundaries.

Plan visuals are part of that plan, not another gate: require focused code
context and a small design graph for `complex` work; use them for `standard`
work when a change crosses components or the flow would otherwise be unclear.
Omit them for `light` work unless the developer requests them.

## Subagents

Start requirement refinement, approved-requirement ticket breakdown, and
selected-ticket planning in fresh, bounded worker contexts. Start evidence,
implementation, and verification work in fresh contexts too. The coordinator
never performs those stages itself; it supplies the smallest sufficient
artifact paths and turns worker results into a concise user-facing summary.

Give a subagent only the relevant artifact paths, explicit scope, and required
output. It returns artifact paths, concise evidence, risks or blocks, and a
recommended next action. It cannot update state, approvals, scope, or completion.

Keep user decisions, tiny work, routing, and all workflow-state changes with
the coordinator.

## Worker result and user view

Every worker returns at most five items: status, one-sentence summary, exact
artifact path, open decision or block, and one next action. The coordinator
shows the same result to the user in plain language. Include a clickable file
link when the client supports it and always repeat the exact relative path;
do not ask the user to interpret raw JSON or search for the file.

## Context budget

Target the coordinator at under 40% of available context. Treat this as an
operating target, not an exact meter: do not load full worker reports, source
output, logs, or old conversations into the coordinator. At each gate, retain
only artifact IDs and paths, approval state, unresolved decisions, and the
next action in `current.json`. If context is becoming large before a gate,
write the worker artifact and state snapshot, then resume in a fresh
coordinator context.

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
- Safety or production blocker: suspend active work and let the coordinator route recovery.

Queued items never silently modify the active requirement or ticket.

## Git safety

Preserve unrelated changes. Commit only when requested. Never force-push, reset,
or delete material without explicit authorization.
