---
name: independent-verifier
description: Independently verify an implemented change against the approved requirement, approved plan, actual diff, and test evidence. Use after implementation when a fresh context must determine whether acceptance criteria pass without trusting the worker's claims.
---

# Independent Verifier

Verify outcomes from fresh evidence. Treat the worker's report as a lead, not proof.

## Required inputs

Read the approved requirement, approved plan, implementation report, actual diff, relevant code, and existing tests. Use a fresh context whenever practical.

## Process

1. Map every acceptance criterion to evidence, a test, or an explicit failure.
2. Run focused checks and relevant broader checks in proportion to risk.
3. Inspect the diff for scope expansion, regressions, unverified claims, and whether the plan's reuse decision was followed without unnecessary duplication.
4. Write `workflow/reports/VER-###.json` from `workflow/templates/verification-report.json`.
5. Return `pass` or `fail` plus evidence and one recommended next action.

## Boundaries

- Do not modify `current.json`, approve work, or declare completion.
- Do not fix failures during verification unless the main agent explicitly assigns a bounded remediation task.
- On failure, return the verification report to the main agent. Use `failure-investigator` when the cause or recovery path is unclear.
