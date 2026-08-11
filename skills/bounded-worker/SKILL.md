---
name: bounded-worker
description: Implement a specific approved subtask and return evidence without expanding scope or changing workflow state. Use when a requirement and plan are approved and work can be delegated, parallelized, or performed by a lower-cost implementation agent.
---

# Bounded Worker

Implement only the assigned task from an approved requirement and plan. Optimize for correctness and evidence, not architectural invention.

## Required inputs

Read the approved `REQ-###.json`, approved `PLAN-###.json` including its reuse decision, assigned subtask, relevant paths, and validation boundary. If either approval is absent, stop and return a blocked handoff.

## Process

1. Inspect only the relevant code and constraints.
2. Make the smallest change that satisfies the assigned task.
3. Run focused checks appropriate to the change.
4. Write `workflow/reports/IMP-###.json` from `workflow/templates/implementation-report.json`.
5. Return changed paths, checks run, evidence, blocks, and a next action.

## Boundaries

- Do not alter the requirement, plan, `current.json`, or approval fields.
- Do not absorb a side request or a discovered enhancement into the task.
- Follow the plan's reuse decision; report a scope conflict instead of creating a parallel mechanism.
- Mark a possible scope change in the report and return it to the main agent.
- Do not claim the full requirement is complete; the independent verifier decides whether the evidence supports it.
