---
name: workflow-orchestrator
description: Coordinate multi-stage work with a developer-selected intake route, durable state, fresh bounded specialists, approval gates, and interruption handling. Use when a request may need investigation, planning, implementation, verification, or complex parallel delivery.
---

# Workflow Orchestrator

Use the project `AGENTS.md` as the operating contract and
`workflow/state/current.json` as the active snapshot. The coordinator routes,
asks users, and updates state; it does not investigate source, run tests,
browse, or make technical design decisions.

## Step 0: intake route

Before creating a worker for a root code-changing request, recommend one route:

```text
answer | investigate | light fix | full plan | queue
```

Include a one-sentence reason, material risk, and expected worker count. Wait
for the developer's choice unless it is already explicit. Record the choice in
`current.json` and do not silently turn `fix now` into full planning.

- **Answer**: no code change or worker.
- **Queue**: use `interruption-queue`; resume active work unchanged.
- **Investigate**: create one read-only evidence assignment, then return here.
- **Light fix**: one fresh fast-audit/micro-plan worker. It may implement only
  within the light-route conditions in `AGENTS.md`; otherwise return `blocked`
  with a full-plan recommendation.
- **Full plan**: use the operating loop.

## Full-plan operating loop

1. Fresh `requirement-refiner`: audit intent and bounded evidence; use
   `$requirement-interview` for one material decision at a time.
2. Obtain requirement approval and confirm the Git baseline.
3. Fresh `story-breakdown`: create dependency-aware tickets; select one.
4. Fresh `implementation-planner`: write the selected ticket plan.
5. Obtain plan approval.
6. Fresh `bounded-worker`: implement the approved mission.
7. Fresh `independent-verifier`: verify against the requirement, plan, and diff.
8. On failure, use `failure-investigator`; rework only when the route is clear.

For `complex` work, plan every ready slice, use one fresh
`parallel-execution-pack` worker, and obtain execution-pack approval before any
isolated implementation worker starts. Integrate verified work in approved
order and independently verify the integrated diff.

## Assignment and handoff

Create or describe `workflow/templates/worker-assignment.json` for every fresh
worker. Name one goal, no more than the budgeted named sources, a required
artifact, a report limit, and a stop condition. Pass source paths and purposes,
not transcripts, logs, broad scans, or repeated screenshots. Use the smallest
budget that can answer the decision.

Workers return five items only: status, one-sentence summary, exact artifact
path, open block, and next action. Keep detailed reasoning in the artifact and
show the user a plain-language summary with its path. Use `stage-handoff` only
when a new context needs durable stage evidence, not for every short message.

At each gate, retain only route, IDs, paths, approvals, unresolved decisions,
and next action in `current.json`. If the coordinator becomes noisy, save the
worker artifact and resume from that snapshot in a fresh coordinator context.

## Interruption and recovery

Classify new input before changing active work: answer a clarification, queue
unrelated work, investigate an active failure, or suspend for a safety/data
risk. A queued item never changes an approved requirement silently. Do not
advance from incomplete, stale, or contradictory evidence; return `blocked` and
route only the missing decision or investigation.
