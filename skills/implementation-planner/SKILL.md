---
name: implementation-planner
description: Create a repository-grounded implementation plan from an approved requirement contract. Use after requirement approval when Codex must inspect the actual codebase, identify constraints, define tests, and produce a plan without silently changing scope.
---

# Implementation Planner

Turn an approved requirement into a bounded, evidence-based plan. Treat the requirement as authoritative; codebase facts may challenge it, but may not silently reinterpret it.

## Inputs

Read the approved `REQ-###.json`, relevant repository files, and `workflow/state/current.json`. Do not plan from chat history alone.

## Process

1. Inspect the affected code and identify constraints, existing behavior, test seams, and reusable mechanisms.
2. Record the plan's reuse decision: existing feature or mechanism, files and symbols checked, `reuse`, `extend`, `replace`, or `create_new`, and the reason.
3. If repository evidence is insufficient, use a read-only investigation only as needed; record its report ID. Do not invent a candidate or delegate by default.
4. Create `workflow/plans/PLAN-###.json` from `workflow/templates/plan.json`.
5. List the approach, affected components, ordered steps, tests, risks, and any scope conflict.
6. Return the plan to the main agent for approval.

## Boundaries

- Do not set `approved: true`; only the main agent records plan approval.
- If the codebase makes the requirement ambiguous or infeasible, add the conflict to `scope_changes` and return to requirement refinement.
- A `create_new` decision needs a reason; implementation must follow the recorded decision.
- Keep implementation tasks small enough to assign independently when useful.
- Do not start implementation or change the requirement while planning.

## Handoff

Return the plan ID, evidence paths, scope conflicts, risks, and one next action. A plan is ready for approval only when it satisfies the approved requirement and identifies how verification will be performed.
