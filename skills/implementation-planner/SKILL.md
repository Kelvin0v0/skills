---
name: implementation-planner
description: Create a repository-grounded implementation plan from an approved requirement contract. Use after requirement approval when Codex must inspect the actual codebase, identify constraints, define tests, and produce a plan without silently changing scope.
---

# Implementation Planner

Run in a fresh worker context. Turn an approved requirement and selected ticket
into a bounded, evidence-based plan. Treat the requirement as authoritative;
codebase facts may challenge it, but may not silently reinterpret it.

## Inputs

Read the approved `REQ-###.json`, selected `TICKET-###.json`, relevant
repository files, and `workflow/state/current.json`. Do not plan from chat
history alone.

## Process

1. Inspect the affected code and identify constraints, existing behavior, test seams, and reusable mechanisms.
2. Identify only the applicable `design_lanes`: `ui_ux`, `frontend`, `backend`, and/or `data_sql`. Translate the approved UI/UX contract into components, states, data rendering, interactions, API flow, service flow, or migration details as applicable. Leave irrelevant lanes empty; use one plan unless independent complex work benefits from a split.
3. Record the plan's reuse decision: existing feature or mechanism, files and symbols checked, `reuse`, `extend`, `replace`, or `create_new`, and the reason.
4. Record the implementation contract: acceptance-criterion references, one bounded implementation mission, observable acceptance checks, design trade-offs, expected files and symbols, boundaries, required checks, and future posture. Mark `red_green_refactor` as `when_practical`; if it is not practical, record the strongest focused alternative check and why.
5. Add plan visuals when they help the developer review the design: require concise code context and a small Mermaid graph for `complex` work; use them for `standard` work that crosses components or has an unclear control or data flow; omit them for `light` work unless requested. Show only the relevant existing path/symbol and intended change, never full files or speculative final code.
6. Record future posture only for a known variation supported by repository, ticket, or user evidence; otherwise set `no_future_generalisation`.
7. Inherit the approved root requirement's audit. Run a delta audit only for a new material behavior, scope, reuse/design, integration, data, permission, migration, risk, or boundary uncertainty exposed by inspection. Route user-intent uncertainty to requirement refinement, ticket-scope uncertainty to ticket decomposition, and technical uncertainty to bounded investigation.
8. Discuss implementation alternatives with the developer when useful, but persist the resulting decision in the plan. Return to the coordinator and requirement gate before accepting any material change to behavior, scope, accessibility, permissions, data, or architecture.
9. If evidence is needed, create a bounded read-only assignment with the question, evidence level, allowed sources, stop condition, and report path. Prefer approved artifacts, then repository evidence, then official external sources only when needed. Do not ask the coordinator to research.
10. Create `workflow/plans/PLAN-###.json` from `workflow/templates/plan.json`.
11. For a complex parallel candidate, complete `parallel_safety` with expected changed paths, shared files/symbols/contracts, dependencies, risks, and `execution_status: ready_for_pack` only when no planning blocker remains. Do not declare it safe to run in parallel; the fresh execution-pack worker compares every candidate plan.
12. List the approach, affected components, ordered steps, tests, risks, and any scope conflict.
13. Return the plan to the main agent for approval only when no material uncertainty remains.

## Boundaries

- Do not set `approved: true`; only the main agent records plan approval.
- If the codebase makes the requirement ambiguous or infeasible, add the conflict to `scope_changes` and return to requirement refinement.
- A `create_new` decision needs a reason; implementation must follow the recorded decision.
- Exact file/function mapping is normal worker translation. Return to planning only for a material contract change.
- Do not turn a screenshot into an implementation guess. Follow the approved UI/UX contract and return unresolved hidden behavior to requirement refinement.
- Keep implementation tasks small enough to assign independently when useful.
- Do not start implementation or change the requirement while planning.

## Handoff

Return: status, one-sentence plan summary, exact `workflow/plans/PLAN-###.json`
path, scope conflict or `none`, and one next action. Include evidence paths
only when they affect the approval decision. A plan is ready for approval only
when it satisfies the approved requirement and identifies how verification will
be performed.
