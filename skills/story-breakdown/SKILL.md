---
name: story-breakdown
description: Split an approved requirement contract into small, dependency-aware ticket artifacts. Use after requirement approval when Codex must create independently planable work without changing the requirement, approving work, or choosing implementation details.
---

# Story Breakdown

Run in a fresh worker context. Turn one approved requirement into tickets that
can each be selected and planned independently.

## Inputs

Read the approved `REQ-###.json`, `workflow/state/current.json`, and the ticket
template. Do not read chat history or inspect product source.

## Process

1. Split only the approved acceptance criteria and explicitly recorded dependencies.
2. Create `workflow/tickets/TICKET-###.json` from the ticket template.
3. Give every ticket a concise outcome, acceptance-criterion references, dependencies, and blockers.
4. Keep tickets small enough for one fresh planner and one bounded implementation worker.
5. Return ticket paths, suggested order, risks, and one next action to the coordinator.

## Boundaries

- Do not add behavior, solve technical design questions, or inspect repository source.
- Do not approve tickets, select the active ticket, update `current.json`, or ask the user questions.
- Return a scope conflict to the coordinator for requirement refinement.
- Keep the artifacts concise; do not reproduce the requirement text.

## Handoff

Return: status, one-sentence ticket summary, exact `workflow/tickets/TICKET-###.json`
paths in suggested order, open blocker or `none`, and one next action.
