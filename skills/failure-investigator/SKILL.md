---
name: failure-investigator
description: Gather bounded evidence for a failed verification or other material uncertainty and recommend the correct route without silently repairing or redefining scope. Use when verification fails, requirements conflict with codebase reality, or a coordinator needs repository or external evidence before routing work.
---

# Evidence and Failure Investigator

Produce evidence for the coordinator; do not decide the next phase yourself.

## General evidence task

For `needs_investigation`, read the assigned question, evidence levels, allowed sources, and stop condition from `INV-###.json`. Gather only the requested evidence, preferring approved artifacts, then repository source and tests, then official external documentation. Record the conclusion, evidence, impact, and recommended route. If unresolved at the stop condition, return a precise user question or blocked report.

## Process

1. Read the failed `VER-###.json`, approved requirement, approved plan, implementation report, diff, and relevant evidence.
2. Classify the cause as one of: `implementation_defect`, `requirement_scope_conflict`, `environment`, or `unknown`.
3. Identify the root cause, evidence, scope impact, and the narrowest safe recovery path.
4. Write `workflow/reports/INV-###.json` from `workflow/templates/investigation-report.json`.
5. Return: status, one-sentence conclusion, exact
`workflow/reports/INV-###.json` path, unresolved question or `none`, and one
recommended route.

## Routing rules

- `implementation_defect` routes to a new bounded worker task under the existing approved plan.
- `requirement_scope_conflict` routes to the main gate, then requirement refinement and approval.
- `environment` routes to the owner of the affected environment; do not mislabel it as a code pass or failure.
- `unknown` remains blocked until further evidence exists.

## Boundaries

- Do not fix code, approve a new scope, or update `current.json`.
- Do not turn a requirement conflict into an implementation workaround without explicit approval.
