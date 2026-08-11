---
name: interruption-queue
description: Classify, record, and later resolve questions, bugs, ideas, and side requests without silently changing the active requirement. Use when a user raises non-blocking work during another workflow stage or when interruptions must survive context compaction.
---

# Interruption Queue

Use `workflow/state/queue.json` as the durable buffer for work that should be remembered but does not belong in the current execution path. A queue item may influence future work, but it cannot silently rewrite the active requirement.

## Classify first

- **blocker** — the active work cannot safely continue without resolving this item; handle it now.
- **safety** — the item indicates imminent data loss, security, or unsafe external impact; pause the affected action and handle it now.
- **normal** — useful but non-blocking; record it and continue the active stage.
- **idea** — a future improvement or alternative; record it unless the user explicitly promotes it.

If the item changes acceptance criteria, scope, architecture, or safety assumptions, classify it as `requirement_change`. Keep the active ticket unchanged, then route the item through a fresh `requirement-refiner`, user approval, and fresh `story-breakdown`.

## Queue record

Append an object to `workflow/state/queue.json`:

```json
{
  "id": "Q-001",
  "type": "bug",
  "summary": "Login sometimes redirects twice",
  "details": "Reproduction steps or the user's exact question",
  "reported_during": "implementation",
  "related_requirement": "REQ-001",
  "related_ticket": "TICKET-002",
  "priority": "normal",
  "blocks_current_work": false,
  "status": "pending",
  "created_at": "2026-08-11T00:00:00Z",
  "resolved_at": null,
  "resolution": null
}
```

Required values are an ID, type, summary, stage, priority, blocking flag, status, and creation time. Use `question`, `bug`, `idea`, `task`, or `requirement_change` for `type`; use `pending`, `in_progress`, `resolved`, `dismissed`, or `promoted` for `status`.

## Queue hygiene

- Use the next stable `Q-###` ID; do not reuse IDs.
- Preserve the user's meaning and record evidence or reproduction details when available.
- Link the item to the active requirement, ticket, and stage so it can be triaged later.
- Deduplicate against pending items before appending.
- Update `updated_at` whenever the file changes.
- Do not bury a blocker in the queue merely to preserve momentum.

## Later processing

When processing queued work:

1. Select a pending item and set it to `in_progress`.
2. Re-read the related requirement and verify that the item is still relevant.
3. If it changes the active requirement, set it to `promoted` and route it through a fresh `requirement-refiner`, user approval, and fresh `story-breakdown`.
4. Otherwise resolve or dismiss it with a concise evidence-backed explanation.
5. Record the resolution and timestamp; retain the item as history.

If a queued bug reveals a current defect, create a focused remediation path and link it to both the queue item and the affected requirement. Do not modify the active plan without recording why.

Return: queue action, one-sentence summary, exact `workflow/state/queue.json`
path, active-work impact or `none`, and one next action.
