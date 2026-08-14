---
name: stage-handoff
description: Create or consume a concise, traceable JSON contract between workflow stages or fresh specialists. Use when the receiving context needs durable evidence rather than chat history.
---

# Stage Handoff

Use a handoff only when work crosses a meaningful stage or fresh context. It is
a receiving contract, not a transcript. Store canonical JSON under
`workflow/handoffs/` with: ID, requirement ID, from/to stage, status, objective,
acceptance criteria, completed evidence, artifact paths, approved decisions,
open questions, material risks, one next action, and timestamp.

Use `ready` only when the receiver can act; use `blocked` for a missing
decision/evidence and `superseded` for a replacement. Link files and state their
purpose instead of embedding logs, screenshots, conversation, or reasoning.
Keep only decisions relevant to the next stage and follow the assigned word
limit; a material omission is `blocked`.

Before acting, confirm the handoff is current, artifacts exist, IDs and
acceptance criteria match active work, and open questions are resolved. Stop on
stale or conflicting evidence. Only the coordinator validates a handoff and
updates `current.json`.
