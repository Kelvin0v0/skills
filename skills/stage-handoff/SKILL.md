---
name: stage-handoff
description: Create and consume concise, traceable handoff artifacts between workflow stages or specialist agents. Use when the receiving context must continue from durable state without rereading the entire conversation or trusting unstructured summaries.
---

# Stage Handoff

Use a handoff whenever work crosses a stage boundary or moves to a fresh context. The handoff is a contract for the receiving agent, not a transcript of the previous context.

## Required handoff fields

Store gate-relevant handoffs as JSON under `workflow/handoffs/`, using a stable ID and a descriptive filename. Markdown may supplement a handoff for people, but JSON is the canonical machine-readable contract. Include:

```json
{
  "id": "HO-001",
  "schema_version": "1.0",
  "requirement_id": "REQ-001",
  "from_stage": "discovery",
  "to_stage": "specification",
  "author_role": "implementation-planner",
  "status": "ready",
  "objective": "One-sentence desired outcome",
  "acceptance_criteria": ["Observable condition"],
  "completed": ["Evidence-backed work already done"],
  "artifacts": ["relative/path/to/artifact"],
  "decisions": ["Decisions already approved"],
  "open_questions": ["Questions that must be resolved"],
  "risks": ["Known risks or assumptions"],
  "next_action": "The first concrete action for the receiving stage",
  "created_at": "2026-08-11T00:00:00Z"
}
```

Use `status: "ready"` only when the next stage has enough information to begin. Use `blocked` when a decision or artifact is missing, and `superseded` when a newer handoff replaces it. Never silently edit a completed handoff to change the requirement; create a new revision or a new handoff.

The receiving specialist may consume the handoff, but only the main agent may validate it and update `workflow/state/current.json`.

## Writing rules

- State facts separately from assumptions.
- Prefer links to files, diffs, test output, or other evidence over copied logs.
- Keep `next_action` singular and executable.
- Preserve requirement and artifact IDs across stages.
- Include only decisions relevant to the receiving stage.
- Record rejected alternatives only when they prevent likely rework.
- Keep the summary short enough to load into a clean context.

## Receiving protocol

Before acting on a handoff:

1. Confirm its `status` is `ready`.
2. Verify the referenced artifacts exist and are still current.
3. Compare the requirement ID and acceptance criteria with the active task.
4. Resolve `open_questions` before making behavior-changing decisions.
5. Perform `next_action`, then create the next handoff with evidence.

If the artifact is stale, incomplete, or inconsistent with the active requirement, stop at the boundary and return a `blocked` handoff describing the discrepancy. Do not reconstruct missing intent from memory.

## Specialist handoffs

Give a specialist the smallest sufficient input: the active requirement, relevant paths, constraints, and requested deliverable. Ask it to return changed paths, checks run, decisions made, open risks, and a next action. Keep exploratory detail in files rather than flooding the coordinator context.
