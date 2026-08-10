---
name: workflow-orchestrator
description: Coordinate multi-stage work with explicit gates, durable state, context-isolated specialists, structured handoffs, and interruption handling. Use when a request spans discovery, specification, planning, implementation, verification, review, or other stages where conversation order and context growth could cause drift.
---

# Workflow Orchestrator

Use this skill to keep an active requirement stable while work moves through small, verifiable stages. Treat the project `AGENTS.md` as the operating contract and use the filesystem for durable state.

## Operating loop

1. **Intake** — Restate the desired outcome, identify the active requirement, and separate in-scope work from side requests.
2. **Discover** — Inspect the relevant files and collect evidence. Record constraints instead of guessing.
3. **Specify** — Turn the request into observable acceptance criteria and list unresolved decisions.
4. **Plan** — Select the smallest implementation sequence, affected files, checks, and rollback considerations.
5. **Implement** — Make incremental changes. Keep unrelated edits untouched.
6. **Verify** — Run focused checks after meaningful changes, then the relevant broader checks.
7. **Review** — Compare the result against the acceptance criteria and repository standards. Review the diff for accidental scope expansion.
8. **Complete** — Report changed artifacts, verification evidence, remaining risks, and queued follow-ups.

For a small, low-risk request, compress stages only when the omitted gate cannot change scope or correctness. Say which gates were compressed in the final handoff.

## Gate protocol

Before advancing a stage, record:

- the stage that just finished;
- the evidence or artifact it produced;
- unresolved decisions and who must decide them;
- the next stage and its first concrete action.

Stop and ask for direction when an unresolved decision would materially change behavior, scope, external impact, or data safety. Do not use a later implementation decision to silently resolve an earlier requirement ambiguity.

## Context isolation

Use a fresh specialist context for substantial exploration, implementation, verification, or review when the current context is becoming noisy or the work benefits from independence. Give the specialist only:

- the active requirement and acceptance criteria;
- the relevant paths or artifacts;
- constraints and known risks;
- the requested output and validation boundary.

Require the specialist to return a concise structured handoff. Store large logs or generated artifacts on disk and return paths plus a distilled conclusion. Do not ask a reviewer to rely on the implementer's unfiltered reasoning.

## Interruption routing

When a user raises a question, bug, or idea during active work, classify it before changing the current plan:

- **Blocker** — handle now if the active work cannot safely continue.
- **Safety/data risk** — pause the affected action and resolve now.
- **Non-blocking** — append it to `workflow/state/queue.json` and continue.
- **Requirement-impacting** — pause, re-enter intake/specification, and update the active requirement explicitly.

Use the `interruption-queue` skill for the queue record and the `stage-handoff` skill for cross-stage artifacts.

## Recovery

If a specialist fails, returns an incomplete artifact, or disagrees with the active requirement:

1. Preserve the failed output for diagnosis when it is useful.
2. Mark the handoff as incomplete and state the missing evidence.
3. Re-run only the missing stage or route the issue to the coordinator.
4. Do not advance based on an ambiguous or stale handoff.

## Completion checklist

- Acceptance criteria are satisfied or explicitly marked incomplete.
- Relevant checks have run and their results are recorded.
- The diff contains no unexplained scope expansion.
- Durable decisions and follow-ups have been stored.
- Non-blocking interruptions remain in the queue.
- The final response distinguishes completed work, verification, risks, and next actions.
