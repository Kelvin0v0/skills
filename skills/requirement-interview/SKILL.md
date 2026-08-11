---
name: requirement-interview
description: Ask and record one material root-requirement decision at a time using a requirement-refiner's ranked question. Use in the coordinator chat after requirement research identifies needs_user_input and before requirement approval, tickets, or planning.
---

# Requirement Interview

Run in the coordinator chat, not a worker context. Turn the refiner's next
material question into one clear developer decision without researching,
designing, or advancing the workflow.

## Inputs

Read the active `REQ-###.json` and only the refiner's next question,
recommended answer, and reason. Do not inspect repository source, tests, or
external material; return those facts to bounded evidence work.

## Process

1. Ask exactly one decision question.
2. State the recommended answer and one brief reason.
3. Wait for the developer's answer; do not ask a follow-up in the same message.
4. Record the answer under `clarification_audit.interview.answers` in the
   requirement artifact and clear `next_question`.
5. Start a fresh `requirement-refiner` run with the revised artifact. Do not
   approve the requirement or start tickets until its audit closes.

## Boundaries

- Do not ask for facts the refiner or an evidence worker can discover.
- Do not use a questionnaire, make technical design decisions, or assume an
  unanswered decision.
- Do not update `current.json`, select tickets, approve work, or declare
  completion.
