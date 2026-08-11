# Workflow Factory Operating Contract

This repository defines a staged, context-aware workflow for turning user intent into verified changes. Treat this file as the project-level operating contract for Codex and other agents working in the repository.

## Core principles

- Keep the active requirement stable while work is in progress.
- Move work through explicit stages instead of letting conversation order determine execution order.
- Keep the main agent small and decision-focused; isolate substantial reasoning in bounded specialist contexts.
- Persist compact contracts and evidence in files; do not rely on chat history alone.
- Every successful boundary must reduce the remaining possibility space. If new evidence creates ambiguity or expands scope, send it upward to the main gate.
- Questions, bugs, and ideas raised during active work must be classified before they can change the active requirement.

## Authority model

The **main agent** is the only authority that may update `workflow/state/current.json`, approve a requirement or plan, advance the workflow phase, or declare completion.

Specialists may inspect, implement, test, and write evidence artifacts. Their reports are evidence and recommendations, never authorization. Do not allow agent-to-agent state changes without the main agent validating the report and recording the transition.

Use logical roles, not permanently running agents:

- **Requirement refiner** clarifies intent and writes the requirement contract.
- **Implementation planner** inspects the codebase and writes an implementation plan for an approved requirement.
- **Bounded worker** implements an approved task without redefining scope.
- **Independent verifier** tests the actual change against the approved requirement and plan in a fresh context.
- **Failure investigator** classifies a failed verification and recommends the recovery path.

Use a specialist when work is independent, bounded, likely to pollute the main context, or can be performed cost-effectively by a lower-capability model. Keep work with the main agent when it is tiny, depends directly on the current decision, or requires user approval.

## Default lifecycle and gates

`intake -> requirement refinement -> requirement approval -> planning -> plan approval -> implementation -> independent verification -> review -> completion`

Do not implement before both the requirement and plan are approved. Do not complete before independent verification and review provide evidence that the acceptance criteria are satisfied.

| Gate | Required evidence | Main-agent decision |
| --- | --- | --- |
| Requirement approval | `REQ-###.json` with clear acceptance criteria and no material unresolved question | approve, refine again, or defer |
| Plan approval | `PLAN-###.json` grounded in the repository and within requirement scope | approve, revise, or return to refinement |
| Verification | `VER-###.json` with test and acceptance-criterion results | complete, return defect to worker, or investigate |
| Completion | verification evidence plus final review | mark complete and report residual risks |

For a small, low-risk request, compress stages only when the omitted gate cannot change scope or correctness. Record which gate was compressed and why.

## Durable state and artifacts

Keep durable workflow state below `workflow/`:

- `workflow/state/current.json` is the small authoritative snapshot of the active requirement, phase, approvals, and next action.
- `workflow/state/queue.json` contains non-blocking interruptions.
- `workflow/requirements/REQ-###.json` contains approved or pending requirement contracts.
- `workflow/plans/PLAN-###.json` contains implementation plans linked to a requirement.
- `workflow/reports/` contains implementation, verification, and investigation reports.
- `workflow/handoffs/` contains stage-to-stage handoff artifacts when a task crosses contexts.
- `workflow/templates/` provides the canonical schemas for new artifacts.

The filesystem is the source of truth for durable state. Chat is a transport layer, not the only record. Keep `current.json` small; store detailed reasoning and evidence in linked artifacts rather than a giant memory file.

## Stage rules

1. Intake: restate the request, identify the desired outcome, and record unknowns.
2. Requirement refinement: turn the user's intent into a testable `REQ-###.json`; do not design implementation yet.
3. Requirement approval: the main agent obtains the required user decision and records approval.
4. Planning: inspect the actual codebase and write a `PLAN-###.json` constrained by the approved requirement.
5. Plan approval: the main agent obtains approval before implementation.
6. Implementation: give a bounded worker only the approved requirement, approved plan, and assigned subtask. The worker writes an implementation report.
7. Independent verification: use a fresh context to test the requirement, plan, and actual diff. The verifier writes a verification report and does not trust worker claims.
8. Failure investigation: classify a failed verification as an implementation defect, requirement/scope conflict, environment issue, or unknown. Route defects to the worker; route requirement conflicts to the main gate.
9. Review and completion: review the result against the specification and repository standards. The main agent alone records completion.

## Interruption policy

When a user asks a side question, reports a bug, or proposes an idea during an active stage:

- Handle it immediately only if it blocks the active work or creates an immediate safety/data-loss risk.
- Otherwise record it in `workflow/state/queue.json` using the interruption-queue skill.
- A queued item must not silently modify the active requirement.
- If later investigation shows that the item affects the active requirement, promote it through requirement refinement and the relevant approval gate before changing course.

## Handoff policy

Use the stage-handoff skill for cross-context work. A handoff must state the active requirement, current stage, completed work, evidence, open decisions, known risks, and the exact next action. Return summaries and artifact paths to the coordinator; avoid copying large exploratory logs into the next context.

## Approval and Git safety

- Ask for user direction when a missing decision would materially change scope, behavior, or external impact.
- Do not claim completion without verification evidence.
- Do not rewrite or discard unrelated user changes.
- Use small, meaningful commits when commits are requested or clearly part of the repository workflow.
- Never force-push, reset, or delete material without explicit authorization.
