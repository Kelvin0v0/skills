---
name: implementation-planner
description: Create a repository-grounded implementation plan from an approved requirement contract. Use after requirement approval when Codex must inspect the actual codebase, identify constraints, define tests, and produce a plan without silently changing scope.
---

# Implementation Planner

Turn an approved requirement into a bounded, evidence-based plan. Treat the requirement as authoritative; codebase facts may challenge it, but may not silently reinterpret it.

## Inputs

Read the approved `REQ-###.json`, relevant repository files, and `workflow/state/current.json`. Do not plan from chat history alone.

## Process

1. Inspect the affected code and identify constraints, existing behavior, and test seams.
2. Create `workflow/plans/PLAN-###.json` from `workflow/templates/plan.json`.
3. List the approach, affected components, ordered steps, tests, risks, and any scope conflict.
4. Return the plan to the main agent for approval.

## Boundaries

- Do not set `approved: true`; only the main agent records plan approval.
- If the codebase makes the requirement ambiguous or infeasible, add the conflict to `scope_changes` and return to requirement refinement.
- Keep implementation tasks small enough to assign independently when useful.
- Do not start implementation or change the requirement while planning.

## Handoff

Return the plan ID, evidence paths, scope conflicts, risks, and one next action. A plan is ready for approval only when it satisfies the approved requirement and identifies how verification will be performed.
