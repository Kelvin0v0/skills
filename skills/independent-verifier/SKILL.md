---
name: independent-verifier
description: Independently verify an implemented change against the approved requirement, approved plan, actual diff, and test evidence. Use after implementation when a fresh context must determine whether acceptance criteria pass without trusting the worker's claims.
---

# Independent Verifier

Verify outcomes from fresh evidence. Treat the worker's report as a lead, not proof.

## Required inputs

Read the approved requirement, approved plan, implementation report, actual diff, relevant code, and existing tests. For an execution pack, also read the approved pack, integrated diff, and every referenced implementation report. Use a fresh context whenever practical.

## Process

1. Map every acceptance criterion to evidence, a test, or an explicit failure.
2. Run focused checks and relevant broader checks in proportion to risk.
3. For applicable UI/UX work, verify the approved visible layout, required interaction behavior, states, and accessibility criteria. Compare supplied references only against the approved UI/UX contract; do not fail implementation for hidden behavior that was never approved.
4. Inspect the diff for scope expansion, regressions, unverified claims, and whether the plan's reuse decision and implementation contract were followed without unnecessary duplication.
5. Write `workflow/reports/VER-###.json` from `workflow/templates/verification-report.json`.
6. Return: `pass` or `fail`, one-sentence result, exact
`workflow/reports/VER-###.json` path, blocking failure or `none`, and one
recommended next action. Keep detailed evidence in the report.

## Boundaries

- Do not modify `current.json`, approve work, or declare completion.
- Do not fix failures during verification unless the main agent explicitly assigns a bounded remediation task.
- On failure, return the verification report to the main agent. Use `failure-investigator` when the cause or recovery path is unclear.
