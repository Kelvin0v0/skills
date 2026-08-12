---
name: parallel-execution-pack
description: Compare approved candidate ticket plans and prepare a safe parallel execution pack with conflict, dependency, worktree, integration, and verification evidence. Use only for complex multi-slice work after individual planning and a developer-confirmed Git baseline.
---

# Parallel Execution Pack

Run in a fresh, read-only worker context. Compare plan artifacts; do not edit
source, create worktrees, merge, approve the pack, or resolve technical
conflicts.

## Inputs

Read the approved requirement, every in-scope ticket and its planning status,
the ready candidates' plan artifacts, the confirmed Git baseline reference, and
the exact integration boundary. Do not read chat history.

## Process

1. Classify every in-scope slice in `slice_schedule` as `ready`, `blocked`, or
   `deferred`. Reject the pack if a ready slice has no completed plan.
2. Compare every ready plan's acceptance criteria, expected changed paths,
   shared files/symbols, UI/API/data contracts, dependencies, and risks.
3. Identify safe dependency-ready parallel batches and execution waves. Prefer
   sequential work if independence is not evidenced.
4. Record integration order, regression checks after each merge, whether a
   developer manual check is required, and whether final MR review is required.
5. Record every unresolved shared-contract conflict or developer decision. Do
   not select a technical solution on the coordinator's behalf.
6. Create `workflow/execution-packs/EXEC-###.json` from
   `workflow/templates/execution-pack.json`.
7. Return the compact pack and exact decision or `none` to the coordinator.

## Boundaries

- Require a confirmed Git baseline; otherwise return a sequential-only
  recommendation.
- Never allow one planned ready slice to bypass the execution pack. The first
  worker starts only after every ready slice is planned and the complete pack is
  approved by the developer.
- Treat shared API, data, UI, design, or overlapping-file discoveries as pack
  conflicts, not worker-local decisions.
- Never mark the pack approved; the coordinator obtains developer approval.
- Do not make a parallel batch merely to increase concurrency.
- Later independent or deferred slices may be planned while an approved wave
  runs, but require a newly approved pack before implementation.

## Handoff

Return: status, one-sentence execution summary, exact
`workflow/execution-packs/EXEC-###.json` path, shared conflict or `none`, and
one next action.
