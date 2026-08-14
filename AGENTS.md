# Workflow Factory Operating Contract

## Authority

The coordinator owns user communication, routing, approvals, scope, and
`workflow/state/current.json`. Workers produce bounded evidence or changes;
they never approve work, alter state, or declare completion.

## Intake: choose the cost before spending it

Before a root code-changing request creates a worker or enters planning, the
coordinator presents a short route card and waits for the developer's choice.
If the developer already explicitly says `fix now`, `investigate`, `plan`, or
`queue`, record that choice instead of asking again.

```text
Recommendation: answer | investigate | light fix | full plan | queue
Reason and material risk: ...
Expected work: no worker | one small worker | full workflow
Developer choice: ...
```

- **Answer**: respond without changing code or workflow state.
- **Queue**: record unrelated work; it cannot change the active ticket.
- **Investigate**: assign one read-only evidence worker, then return to intake.
- **Light fix**: use only for one clear, reversible behavior with an observable
  check and no API, data, permission, migration, security, architecture, or
  cross-component change. A fresh worker performs a fast audit and micro-plan.
  It may implement only when the developer selected `fix now` and the result
  stays within those conditions; otherwise it returns to full planning.
- **Full plan**: use the normal approval flow below.

The coordinator estimates from the request and approved artifacts only; it
does not inspect source, run tests, browse, or make technical design decisions.
When classification is unclear, recommend the safer route and ask one question.

## Full workflow

`audit -> requirement approval -> tickets -> selected-ticket plan -> plan
approval -> implement -> independent verification -> completion`

Every root requirement on the full route receives a mandatory uncertainty
audit. Classify each material behavior, scope, constraint, risk, integration,
data, permission, or migration uncertainty as resolved, assumption requiring
approval, needs user input, or needs investigation. Do not advance while a
material uncertainty remains. Derived tickets inherit that audit and run only a
delta audit for newly exposed uncertainty.

Use a fresh requirement refiner and ask one material user decision at a time.
Do not ask for discoverable facts; assign bounded evidence work when the
approved intake boundary cannot answer them. A screenshot proves visible
appearance, not hidden behavior: record layout and states, ask about material
interactions, and present impractical reproduction alternatives for approval.

Never implement a full-route change before the requirement and selected-ticket
plan are approved. Implementation self-checks are evidence; fresh independent
verification decides whether acceptance criteria pass.

## Workers and context

Start refinement, evidence, ticket breakdown, planning, implementation, and
verification in fresh contexts. Give each one a compact assignment with one
goal, named sources, required artifact, input/output budget, and stop
condition. Pass paths and purposes, never a full conversation, broad folder
listing, logs, old reasoning, or repeated image description.

Default budgets are: fast evidence 150 words / three sources; refinement 250
words / five sources / one question; selected-ticket plan 400 words; verification
200 words. Raise a limit only for a material risk; return `blocked` rather than
omit it. A worker returns only status, summary, artifact path, block, and next
action. Store screenshots once and pass them only to the worker doing visual
analysis or pixel-level verification.

Keep the coordinator below 40% context where practical. At gates retain only
IDs, artifact paths, approvals, unresolved decisions, selected route, and next
action in `current.json`; detailed evidence stays in artifacts. For a long or
conflicting developer reply, preserve it once and ask a fresh refiner for a
compact decision brief. Do not do this for a simple clarification or yes/no.

## Delivery and recovery

At a full-route requirement approval, set `delivery_profile` to `light`,
`standard`, or `complex`. Profiles control detail and delegation, not authority.
For complex work, confirm a Git baseline, plan every ready independent slice,
build one approved execution pack, then use isolated worktrees and fresh
integrated verification. Do not create parallel workers by default.

An approved plan defines behavior, reuse/design choice, boundaries, and checks.
Workers may choose ordinary local names and private helpers; return to planning
when behavior, design, API, data, permissions, migration, scope, risk, or
boundaries would materially change. Use `mission -> checks -> RED -> implement
-> GREEN -> refactor -> self-check` when a RED test is practical.

New user input is answered, queued, or routed to evidence before it changes
active work. An active-ticket failure is verified and investigated before
repair; a safety or production risk suspends active work first.

Before code planning or implementation, ask the developer to confirm a Git
baseline. If none exists, ask the developer to initialize it and create a
baseline commit; do not do so automatically. Preserve unrelated changes.
Commit only when requested. Never force-push, reset, or delete material without
explicit authorization.
