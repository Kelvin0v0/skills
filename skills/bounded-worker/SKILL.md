---
name: bounded-worker
description: Implement a specific approved subtask and return evidence without expanding scope or changing workflow state. Use when a requirement and plan are approved and work can be delegated, parallelized, or performed by a lower-cost implementation agent.
---

# Bounded Worker

Implement only the assigned task from an approved requirement and plan. Optimize for correctness and evidence, not architectural invention.

## Required inputs

Read the approved `REQ-###.json`, approved `PLAN-###.json` including its reuse decision and implementation contract, assigned subtask, relevant paths, and validation boundary. If either approval is absent, stop and return a blocked handoff.

## Process

1. Read the plan's bounded mission and its observable acceptance checks.
2. Inspect only the relevant code and constraints.
3. Where practical, make the acceptance check fail first (`RED`), implement until it passes (`GREEN`), then refactor without changing behavior. For visual-only, configuration, migration, or other non-testable work, use the plan's stated focused check instead.
4. Make the smallest change that satisfies the whole mission.
5. Run the planned focused checks and a whole-mission self-check against every acceptance check; record both in the implementation report.
6. Write `workflow/reports/IMP-###.json` from `workflow/templates/implementation-report.json`.
7. Return: status, one-sentence result, exact `workflow/reports/IMP-###.json`
path, block or `none`, and one next action. Keep changed paths and checks in
the report unless they are material to the handoff.

## Boundaries

- Do not alter the requirement, plan, `current.json`, or approval fields.
- Do not absorb a side request or a discovered enhancement into the task.
- Follow the plan's reuse decision; report a scope conflict instead of creating a parallel mechanism.
- Follow the implementation contract. Choose ordinary local naming and private helpers from repository conventions; return when a material contract change is needed.
- Treat `GREEN` and the whole-mission self-check as implementation evidence, not independent verification.
- Mark a possible scope change in the report and return it to the main agent.
- Do not claim the full requirement is complete; the independent verifier decides whether the evidence supports it.
- For an approved parallel execution pack, work only in the assigned worktree and do not merge. Report any newly discovered shared file, API/data/UI contract, or dependency; pause rather than changing another slice.
