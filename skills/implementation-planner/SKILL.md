---
name: implementation-planner
description: Create a concise repository-grounded plan for one approved ticket, including reuse choice, acceptance checks, implementation boundaries, and verification. Use after requirement approval when a selected ticket needs technical planning.
---

# Implementation Planner

Run in a fresh worker context for one approved `REQ-###.json` and selected
`TICKET-###.json`. Read only those artifacts, `current.json`, and assigned
repository paths. Treat the requirement as authoritative; code can expose a
conflict but cannot silently change intent.

## Produce `workflow/plans/PLAN-###.json`

1. Inspect relevant behavior, test seams, constraints, and reusable mechanisms.
2. Record the reuse decision: feature/mechanism checked, files and symbols,
   `reuse`, `extend`, `replace`, or `create_new`, and the reason.
3. Record one bounded mission, criterion references, observable checks, expected
   files/symbols, non-goals, trade-offs, and future posture. Use
   `no_future_generalisation` unless concrete evidence supports a variation.
4. Cover only applicable UI/UX, frontend, backend, and data/SQL lanes. For UI,
   translate the approved visible and interaction contract; do not infer hidden
   behavior from a screenshot.
5. Use `RED -> GREEN -> refactor` when practical; otherwise record the
   strongest focused check. Include a tiny code box or Mermaid graph only when
   the approved delivery profile requires it.
6. Run a delta audit only for new material behavior, scope, design/reuse,
   integration, data, permission, migration, risk, or boundary uncertainty.
   Return requirement, ticket, or evidence routing instead of resolving it.

Use no more than five named sources and 400 words unless a material risk needs
more. Do not implement, approve, alter state, or make a parallel-safety claim.
For a complex candidate, record expected changed paths and shared contracts for
the execution-pack worker. Return: status, summary, plan path, scope conflict
or block, and next action.
