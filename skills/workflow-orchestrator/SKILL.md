---
name: workflow-orchestrator
description: Coordinate multi-stage work with explicit gates, durable state, context-isolated specialists, structured handoffs, and interruption handling. Use when a request spans discovery, specification, planning, implementation, verification, review, or other stages where conversation order and context growth could cause drift.
---

# Workflow Orchestrator

Use this skill to keep an active requirement stable while work moves through small, verifiable stages. Treat the project `AGENTS.md` as the operating contract and `workflow/state/current.json` as the authoritative snapshot.

The main agent is a coordinator: it advances workflow state and makes user-facing decisions, but does not investigate source code, run tests, reproduce defects, browse external sources, or make technical design decisions. Specialists create evidence artifacts and recommendations; they never approve scope, modify `current.json`, or declare completion.

## Operating loop

1. **Research, audit, and interview** — Start a fresh, read-only `requirement-refiner` worker with a cited-specification, repository-source, and test boundary to turn user intent into `REQ-###.json`. Route research beyond that boundary to a fresh evidence worker. For each material user decision, use `$requirement-interview` in the coordinator chat: ask one question with a recommendation, wait, record the answer, then re-run the fresh refiner. Do not start tickets until this loop closes.
2. **Approve requirement** — Obtain the required user decision, then update `current.json`.
3. **Confirm version-control baseline** — Before code-changing planning, ask the developer to confirm a Git baseline. If one does not exist, ask the developer to initialize and commit it; never do this automatically. Without confirmation, do not use worktrees or parallel delivery.
4. **Create tickets** — Start a fresh `story-breakdown` worker to create dependency-aware tickets from the approved requirement.
5. **Plan one ticket** — Start a fresh `implementation-planner` worker to inspect the repository and write `PLAN-###.json` for the selected ticket, including focused code context and a Mermaid design graph when the delivery profile requires them.
6. **Approve plan** — Obtain the required user decision, then update `current.json`.
7. **Implement** — Use `bounded-worker` for approved, bounded tasks; retain only the report and artifact paths in the main context.
8. **Verify** — Use `independent-verifier` in a fresh context for implementation changes.
9. **Investigate failures** — Use `failure-investigator` to route a verification failure to a worker or back to refinement.
10. **Review and complete** — Validate evidence, update `current.json`, and report completion or residual risk.

For a small, low-risk request, compress stages only when the omitted gate cannot change scope or correctness. Say which gates were compressed in the final handoff. Never bypass requirement and plan approval for an implementation change.

For `complex` work, classify every in-scope slice as `ready`, `blocked`, or
`deferred` before implementation. Plan every ready slice, then start a fresh
`parallel-execution-pack` worker. It writes
`workflow/execution-packs/EXEC-###.json` with the full slice schedule,
conflicts, safe batches, integration order, and checks. One ready plan is never
permission to implement: the coordinator presents the complete pack for
developer approval before dispatching any worktree. A blocked slice needs
evidence or a developer decision; a deferred slice is outside the current pack.
The worker cannot resolve technical conflicts itself. Later independent slices
may be planned while an approved wave runs, but require a newly approved pack
before they start. Integrate only verified worktrees in approved order, run
regression checks after each merge, then use fresh verification on the
integrated diff.

## Gate protocol

Before advancing a stage, record:

- the stage that just finished;
- the evidence or artifact it produced;
- unresolved decisions and who must decide them;
- the next stage and its first concrete action.

Stop and ask for direction when an unresolved decision would materially change behavior, scope, external impact, or data safety. Do not use a later implementation decision to silently resolve an earlier requirement ambiguity.

For a root requirement, ask questions one at a time through `$requirement-interview`; do not
send a questionnaire. Ask only decisions that evidence workers cannot discover.
After every answer, re-run the requirement refiner so the persisted audit, not
the coordinator's memory, determines whether the next question is needed.

Use this gate sequence: approved requirement -> approved plan -> implementation report -> independent verification report -> completion. If a stage expands the possibility space, route it back to the main gate instead of improvising.

## Context isolation

Use a fresh specialist context for every workflow stage that creates a
requirement, tickets, plan, evidence, implementation, verification, or review.
Do not re-use the coordinator conversation as a worker context. Give the
specialist only:

- the active requirement and acceptance criteria;
- the relevant paths or artifacts;
- constraints and known risks;
- the requested output and validation boundary.

Require the specialist to return five items: status, one-sentence summary,
exact artifact path, open decision or block, and one next action. Store large
logs or generated artifacts on disk. The coordinator presents that result to
the user with a clickable artifact link and the exact path, rather than raw
JSON or a request to search for the file. Do not ask a reviewer to rely on the
implementer's unfiltered reasoning.

Delegate when a task needs evidence, is independent, bounded, or more cost-effective on a lower-capability worker. Keep only user decisions, routing, and workflow administration with the coordinator.

## Context budget

Target coordinator context below 40% of its available window. This is not a
precise counter: use state checkpoints as the control. At every approval,
ticket selection, investigation conclusion, implementation report, and
verification result, keep only artifact paths, approval state, unresolved
decisions, and the next action in `current.json`. Leave research, logs, code
excerpts, and detailed reasoning in the worker artifact. If the coordinator is
becoming noisy before a checkpoint, finish the bounded worker artifact, record
state, and continue in a fresh coordinator context.

## Evidence assignments

For a material unknown, the coordinator creates a bounded, read-only evidence assignment instead of investigating itself. Record it in `INV-###.json` with:

- the exact question and evidence level: `requirement`, `repository`, `external`, or a combination;
- allowed sources and a stop condition;
- evidence and a conclusion about the active requirement or ticket;
- a recommended route: continue, ask the user, refine the requirement, re-plan, or remain blocked.

Gather evidence in this order: approved artifacts, repository source and tests, then official external documentation. If the assignment cannot resolve the question within scope, return a precise user question or blocked report; do not continue researching indefinitely.

## Interruption routing

When a user raises a question, bug, or idea during active work, classify it before changing the current plan:

- **Blocker** — handle now if the active work cannot safely continue.
- **Safety/data risk** — pause the affected action and resolve now.
- **Non-blocking** — append it to `workflow/state/queue.json` and continue.
- **Requirement-impacting** — pause, re-enter intake/specification, and update the active requirement explicitly.

Use the `interruption-queue` skill for the queue record and the `stage-handoff` skill for cross-stage artifacts.

## Recovery

If a specialist fails, returns an incomplete artifact, or disagrees with the active requirement:

1. Preserve the failed output for diagnosis when it is useful.
2. Mark the handoff as incomplete and state the missing evidence.
3. Re-run only the missing stage or route the issue to the coordinator.
4. Do not advance based on an ambiguous or stale handoff.

If verification fails, do not immediately re-run implementation. First create a verification report and use `failure-investigator` when the cause is unclear. Route implementation defects to a bounded worker; route requirement or scope conflicts to the main gate and requirement refinement.

## Completion checklist

- Acceptance criteria are satisfied or explicitly marked incomplete.
- Relevant checks have run and their results are recorded.
- The diff contains no unexplained scope expansion.
- Durable decisions and follow-ups have been stored.
- Non-blocking interruptions remain in the queue.
- The final response distinguishes completed work, verification, risks, and next actions.
