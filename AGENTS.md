# Workflow Factory Operating Contract

This repository defines a staged, context-aware workflow for turning user intent into verified changes. Treat this file as the project-level operating contract for Codex and other agents working in the repository.

## Core principles

- Keep the active requirement stable while work is in progress.
- Move work through explicit stages instead of letting conversation order determine execution order.
- Prefer a fresh specialist context for substantial exploration, implementation, verification, or review.
- Persist important decisions and outputs in files; do not rely on chat history alone.
- Every stage must produce a concise handoff that lets the next stage continue without reconstructing the whole conversation.
- Questions, bugs, and ideas raised during active work must be classified before they can change the active requirement.

## Default lifecycle

Use this sequence unless the task is clearly smaller or the user requests a different process:

`intake -> discovery -> specification -> planning -> implementation -> verification -> review -> completion`

Each stage has an entry condition, an output, and an exit gate. Do not silently skip a gate when the skipped decision could change scope, risk, or acceptance criteria.

## Durable state

Keep durable workflow state below `workflow/`:

- `workflow/state/queue.json` contains non-blocking interruptions.
- `workflow/handoffs/` contains stage-to-stage handoff artifacts when a task needs more than one context.
- Requirement, plan, decision, and verification artifacts should use stable IDs and link back to the active requirement.

The filesystem is the source of truth for durable state. Chat is a transport layer, not the only record.

## Stage rules

1. Intake: restate the request, identify the desired outcome, and record unknowns.
2. Discovery: inspect the relevant repository or source material and record evidence, constraints, and risks.
3. Specification: write testable acceptance criteria. Resolve ambiguity before implementation when it affects behavior.
4. Planning: choose the smallest sequence of verifiable changes and identify the files or seams involved.
5. Implementation: make incremental changes and preserve unrelated user work.
6. Verification: run focused checks after meaningful changes, then the relevant full checks.
7. Review: compare the result against the specification and repository standards; fix discrepancies before completion.
8. Completion: report what changed, what was verified, and any remaining risk. Commit only when the user or repository workflow authorizes it.

## Interruption policy

When a user asks a side question, reports a bug, or proposes an idea during an active stage:

- Handle it immediately only if it blocks the active work or creates an immediate safety/data-loss risk.
- Otherwise record it in `workflow/state/queue.json` using the interruption-queue skill.
- A queued item must not silently modify the active requirement.
- If later investigation shows that the item affects the active requirement, return it through intake and specification before changing course.

## Handoff policy

Use the stage-handoff skill for cross-context work. A handoff must state the active requirement, current stage, completed work, evidence, open decisions, known risks, and the exact next action. Return summaries and artifact paths to the coordinator; avoid copying large exploratory logs into the next context.

## Approval and Git safety

- Ask for user direction when a missing decision would materially change scope, behavior, or external impact.
- Do not claim completion without verification evidence.
- Do not rewrite or discard unrelated user changes.
- Use small, meaningful commits when commits are requested or clearly part of the repository workflow.
- Never force-push, reset, or delete material without explicit authorization.
