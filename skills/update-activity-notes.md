---
name: airtable-activity-notes-updater
description: >
  Convert a pasted email, one-line note, meeting snippet, or full meeting transcript into
  a clean Airtable account activity note, append it to the correct account in Airtable,
  update Engagement Status / Outreach Step / cadence fields when supported, and maintain
  Customer Tasks by creating, deduping, updating, and closing task records when the new
  activity clearly proves work is owed or complete.
---

# Airtable Activity Notes Updater — Task Lifecycle V3

## Purpose

Create a concise Airtable activity note from raw customer/internal context, append it to the correct account, keep account workflow fields accurate, and maintain the dedicated Customer Tasks table.

This skill handles:

- one-line updates
- rough notes
- email threads
- customer emails
- meeting snippets
- full meeting transcripts
- Slack/internal updates
- follow-up emails/messages that should be logged
- task capture from emails, notes, and transcripts
- task check-off when later updates prove the work was done

The skill must prioritize speed, accuracy, and workflow hygiene. Do not over-document.

---

## Shared Contracts

Before running this workflow, apply:

- `contracts/task-lifecycle.md` for task state, matching, deduplication, completion, and reopening.
- `contracts/write-safety.md` for read/draft/write boundaries and draft-versus-sent rules.
- `contracts/untrusted-input.md` for emails, transcripts, pasted notes, and external content.
- `schema/airtable-schema-map.md` for current Airtable IDs and allowed values.

If this skill conflicts with a shared contract, the shared contract wins.

---

# Airtable Source of Truth

Use Airtable as the system to update.

Base:

- Book of Business Management
- Base ID: `app6O8peF5ywLe1GM`

Primary table:

- Accounts
- Table ID: `tblr6UnvfaqfNvwyU`

Task table:

- Customer Tasks
- Table ID: `tblWUzwjGM4pwhAqR`

Supporting table:

- Detailed Notes
- Table ID: `tblI5cCnIY63S6pZq`

## Accounts Fields to Read / Update

| Field | Field ID | Use |
|---|---|---|
| Account Name | `fldOSLvopNOX6ae3Z` | Match the customer account |
| Activity notes | `flddz3lqUmEhLhmN5` | Append the new activity note |
| Engagement Status | `fldyrxDGOzWF3c7wm` | Primary account relationship / engagement state |
| Outreach Step | `fldhX3nTqX4a2eKt8` | Primary outreach/follow-up sequence state |
| Meeting Sync established | `fld8kFpch7M4wGfpQ` | Use `yes` only when a recurring sync/cadence is confirmed |
| Cadence Frequency | `flddcR78KacwfLyk5` | Confirmed cadence frequency |
| Stage | `fldTYypHjPaFcatCi` | Legacy fallback only; do not update unless explicitly asked |
| Task status | `fldaYegYsT0eA3NAK` | Account-level rollup signal from Customer Tasks |
| Last Activity Date | `fld2jD1HJm9RRwNBW` | Airtable record-touch signal, not proof of customer activity |

## Customer Tasks Fields

| Field | Field ID | Use |
|---|---|---|
| Task Title | `fldbPKh3KNG9vbeXS` | Short action-oriented task name |
| Account | `fldMIaXj2PtqHkmNk` | Linked account record |
| Owner | `fldVF6TQNNaPmv8qf` | `Ranjodh`, `Customer`, `AE / AM`, `Support`, `Internal Team`, `Shared`, `Unknown` |
| Status | `fldFbZYJvZUZmrGf2` | `Open`, `In Progress`, `Waiting on Internal Team`, `Waiting on Customer`, `Blocked`, `Done`, `Cancelled`, `Needs Review` |
| Priority | `fldGixN6MmFNAbTdG` | `P1 - Must do`, `P2 - Should do`, `P3 - Monitor` |
| Due Date | `fldm4uWSm23HVxZPC` | Due date only when clearly stated or safely inferred |
| Customer Waiting? | `fldDXgxychawzNCZn` | Checked when customer is waiting on Ranjodh / 6sense / internal owner |
| Source Type | `fldRaStx1IoK4ooqv` | `Email`, `Meeting`, `Transcript`, `Slack / Internal`, `Support`, `Manual`, `Other` |
| Source Date | `fldp9gpKEdSIywADe` | Date of source activity |
| Source Summary | `fldKEtfu2JfA5VTKK` | Why the task exists |
| Source Detail Note | `fldo4zvmVhOXqed2p` | Optional linked Detailed Notes record |
| Related Email / Meeting Summary | `fldhj5zbRG2dwT2sF` | Relevant excerpt or condensed source text |
| Completion Evidence | `fldsWVTfAAagVBTPT` | Evidence used to mark a task complete |
| Completed Date | `fldcOYlYd9dfmyl5K` | Completion date when supported |
| Needs Review | `fld5SXTsSrQNl96t2` | Check when matching/completion is uncertain |
| Last Updated From | `fldg0rM7UT6Jt148a` | Always write `Update Notes` from this skill |

Important:

- Customer Tasks is the execution tracker.
- Activity notes is the historical account narrative.
- Detailed Notes is the structured meeting/activity detail layer.
- Engagement Status and Outreach Step remain the primary account progression fields.
- Stage is legacy context only and must not be updated unless the user explicitly asks.
- For single select fields, write the plain option name, not the object returned by Airtable.

---

# Core Workflow

1. Identify the account from the user message or pasted content.
   - If obvious, proceed.
   - If missing or ambiguous, ask: `Which account should I update this under?`
2. Fetch the account from Airtable and verify the returned Account Name strongly matches.
3. Pull current Accounts values for Activity notes, Engagement Status, Outreach Step, Meeting Sync established, Cadence Frequency, Task status, and legacy Stage.
4. Pull open/actionable Customer Tasks for the account.
   - Include statuses: `Open`, `In Progress`, `Waiting on Internal Team`, `Waiting on Customer`, `Blocked`, `Needs Review`.
   - Exclude statuses: `Done`, `Cancelled` unless the user explicitly asks for closed history.
5. Classify the activity signal.
6. Create the new activity note.
7. Determine account status/cadence changes, including the follow-up hard check.
8. Apply Customer Tasks lifecycle logic:
   - close tasks when completion is proven
   - update tasks when status/owner/priority changed
   - create new tasks when a clear owed action exists
   - dedupe similar open tasks
   - flag ambiguous items for review
9. Apply source idempotency from `contracts/write-safety.md`. Append the note with two line breaks only when the same source is not already logged; otherwise skip the duplicate append and continue only with genuinely new reconciliation.
10. Update the Accounts record and Customer Tasks records.
11. Recalculate Accounts → Task status as a summary signal.
12. Confirm exactly what changed, including task changes and current workflow/cadence state.

---

# Date and Activity Note Format

Every activity note must start with today's date in Asia/Kolkata unless the user explicitly provides another activity date.

Use this format:

```text
DD Month YYYY - [concise activity update]
```

Simple updates:

- Default to one sentence.
- Target 25–45 words.
- Maximum 60 words unless critical details require more.

Meeting transcripts or detailed notes:

- Use a 3–4 sentence summary.
- Include only the top user-owned tasks when clear.
- Maximum 150 words including tasks.

Preserve customer names, product names, technical terms, support ticket references, dates, numbers, deadlines, renewal dates, owners, and explicit customer asks.

Do not invent missing details.

---

# Customer Tasks Lifecycle

## What counts as a task

Create or update a Customer Tasks record only when there is a clear action and a reasonably clear owner.

Good task signals:

- `I will send...`
- `I’ll confirm...`
- `Ranjodh to follow up...`
- `Need to schedule...`
- `Need to send invite...`
- `I’ll check internally...`
- `I’ll get back...`
- `Share update...`
- `Send resources...`
- `Prepare...`
- `Loop in...`
- `Coordinate...`
- `Track the support ticket...`
- `Create / raise a support case...`

Do not create task rows for vague relationship work such as `continue partnership`, `monitor account`, `stay aligned`, or `keep momentum` unless the source includes a concrete next action.

## Owner mapping

| Signal | Owner |
|---|---|
| Ranjodh / I / 6sense CSM owes the action | `Ranjodh` |
| Customer owes the action | `Customer` |
| AE / AM / commercial owner owes the action | `AE / AM` |
| Support owns investigation or resolution | `Support` |
| Product, engineering, ops, or unspecified 6sense team owns it | `Internal Team` |
| Both customer and 6sense need to act | `Shared` |
| Owner is unclear but action is real | `Unknown` and `Needs Review` checked |

## Status mapping

| Source signal | Task Status |
|---|---|
| Clear new action owed by Ranjodh / 6sense | `Open` |
| Work has started but is not complete | `In Progress` |
| Waiting on Support / internal investigation | `Waiting on Internal Team` |
| Waiting on customer input / data / availability | `Waiting on Customer` |
| Issue prevents progress | `Blocked` |
| Completion is clearly proven | `Done` |
| Task no longer needed | `Cancelled` |
| Task is ambiguous, duplicate uncertain, or completion uncertain | `Needs Review` |

## Priority mapping

- `P1 - Must do` when the customer is waiting, the item is a blocker, support escalation, renewal-critical item, executive request, or due within 7 days.
- `P2 - Should do` for normal follow-ups, resources to send, scheduling, and next-step coordination.
- `P3 - Monitor` for low-urgency tracking items that still require visibility.

## Customer Waiting? checkbox

Check `Customer Waiting?` when the customer is waiting on Ranjodh, Support, AE / AM, or another internal 6sense owner.

Usually check it for:

- Ranjodh-owned open tasks from customer emails
- Support / blocker active tasks
- internal investigation tasks promised back to customer
- promised follow-up/resources/update
- scheduling owed by Ranjodh

Usually do not check it for:

- customer-owned tasks where 6sense is waiting on the customer
- internal hygiene tasks not visible to the customer
- monitoring-only tasks

---

# Task Deduplication Rules

Before creating a new Customer Task, search open tasks for the same account.

Dedupe key:

```text
Account + normalized task title/action + owner + open/actionable status
```

Treat these as duplicates unless the source clearly distinguishes them:

- `send workflow docs`
- `share workflow documentation`
- `send over the workflow documentation`

If a duplicate exists:

- update the existing task if the new source adds due date, priority, owner, source summary, or customer-waiting evidence
- do not create a new row
- add relevant source context to the existing task only if it makes the task clearer

If there are multiple similar open tasks and the match is uncertain:

- do not close or merge automatically
- create or update a task as `Needs Review`
- check `Needs Review`
- explain the uncertainty in the confirmation

---

# Task Completion / Check-Off Rules

Every `/update notes` run must compare the new activity against existing open Customer Tasks for the matched account.

## High-confidence completion

Mark an existing task `Done` only when all are true:

1. Same account.
2. Existing task is actionable: `Open`, `In Progress`, `Waiting on Internal Team`, `Waiting on Customer`, `Blocked`, or `Needs Review`.
3. The new activity clearly proves that the specific task was completed.
4. The completion evidence is explicit enough to quote or summarize.
5. Actor, tense, action/object, negation, quotation context, and completion scope pass `contracts/task-lifecycle.md`.

A completion verb alone is not enough. Do not close from quoted or forwarded history, future promises, drafts, negated statements, another actor's unrelated work, or partial completion.

Completion signals include:

- `sent`
- `shared`
- `attached`
- `confirmed`
- `created the ticket`
- `raised the support case`
- `looped in`
- `scheduled`
- `completed`
- `resolved`
- `closed out`
- `followed up`
- `reverted`
- `provided the update`

When marking complete, update:

- Status: `Done`
- Completed Date: activity date or today's date in Asia/Kolkata
- Completion Evidence: exact or concise evidence from the source
- Customer Waiting?: unchecked unless another open linked task still has the customer waiting
- Needs Review: unchecked unless there is still ambiguity
- Last Updated From: `Update Notes`

## Medium-confidence completion

If the new activity implies completion but does not clearly prove it:

- Do not mark `Done`.
- Set Status to `Needs Review` only if the task needs attention.
- Check `Needs Review`.
- Add the possible completion evidence to Completion Evidence.
- Mention it in the confirmation as `Needs review`.

## Low-confidence completion

If the source is vague or unrelated, leave the task open.

Do not close tasks on language like:

- `will send`
- `planning to`
- `need to`
- `working on`
- `checking internally`
- `should have an update soon`

---

# Account-Level Task Status Rollup

After Customer Tasks changes, update Accounts → Task status only as a summary signal.

Use this mapping:

- If one or more linked Customer Tasks are actionable (`Open`, `In Progress`, `Waiting on Internal Team`, `Waiting on Customer`, `Blocked`, or `Needs Review`) → `Open`.
- If one or more linked Customer Tasks exist but all are not started and Airtable has a compatible option → `yet to start`.
- If no linked Customer Tasks remain actionable and Airtable has a compatible option → `closed`.
- If the exact select option is unavailable, do not create a new option unless the user explicitly asks.

Customer Tasks remains the source of truth.

---

# Activity Signal to Status Mapping

| New activity signal | Engagement Status | Outreach Step |
|---|---|---|
| Account newly transitioned and no outreach yet | `Transitioned - not contacted` | `Not started` |
| Intro email sent / first outreach sent | `Intro sent - waiting` | `Intro sent` |
| First follow-up sent after intro | `No response - follow-up needed` | `FU 1` |
| Second follow-up sent | `No response - follow-up needed` | `FU 2` |
| Third follow-up sent | `No response - follow-up needed` | `FU 3` |
| Fourth follow-up sent | `No response - follow-up needed` | `FU 4` |
| Customer replied, but meeting is not booked yet | `Customer replied - scheduling` | `Customer replied` |
| Customer asks to schedule / scheduling is in progress | `Customer replied - scheduling` | `Customer replied` |
| Meeting booked / calendar invite sent / meeting confirmed | `Meeting scheduled` | `Meeting scheduled` |
| Meeting completed, relationship established, no recurring cadence confirmed | `Connected - no cadence` | `Customer replied` |
| Recurring sync / cadence agreed or active | `Cadence established` | `Cadence active` |
| Need additional stakeholders or higher contact | `Multithread required` | `Multithread` |
| Active support issue, blocker, escalation, or unresolved technical issue | `Support / blocker active` | Keep current Outreach Step unless outreach also changed |
| Account intentionally paused / no active outreach planned | `Parked / no active outreach` | `Parked` |
| Churn confirmed, offboarding discussed, or non-renewal confirmed | `Churn / offboarding` | `Parked` |

---

# Follow-Up Stage Progression Rules

Do not advance outreach steps because time passed, because an email was drafted, or because a draft and `/update notes` appear in the same request.

## Draft-versus-sent hard check

Before writing Airtable for a follow-up:

1. Classify the event as Drafted, Sent, Received, Scheduled, Submitted/Raised, or Unknown.
2. Treat Drafted and Unknown as not sent.
3. Require explicit completion evidence such as `I sent the email`, `email sent`, or `this was shared with the customer` before recording a sent touch.
4. If a draft contains a promise, create or update the promised task but do not close the delivery task.
5. Read the current Outreach Step.
6. Only for an explicitly sent follow-up, advance exactly one step:
   - `Intro sent`, `Not started`, or blank after a prior intro exists → `FU 1`
   - `FU 1` → `FU 2`
   - `FU 2` → `FU 3`
   - `FU 3` → `FU 4`
   - `FU 4` → keep `FU 4`; recommend multithread or park only when supported
7. Keep Engagement Status as `No response - follow-up needed` for no-response follow-ups unless a higher-priority signal exists.
8. Show the Outreach Step transition in the confirmation when it changed.

Examples:

- `Write this email and update notes` → log the draft context and promised tasks only; do not mark sent.
- `I sent this email; update notes` → log sent and apply supported progression/completion.
- `I did not send the Pivot email; Ajit will send it and I will confirm with him` → do not close the email task; create or update Ranjodh's confirmation task.

---

# Engagement Status Precedence

When multiple signals appear, use this precedence order:

1. `Churn / offboarding`
2. `Support / blocker active`
3. `Cadence established`
4. `Meeting scheduled`
5. `Customer replied - scheduling`
6. `Multithread required`
7. `No response - follow-up needed`
8. `Intro sent - waiting`
9. `Connected - no cadence`
10. `Parked / no active outreach`
11. `Transitioned - not contacted`

Do not overwrite an active support/blocker or churn/offboarding status with a routine outreach status unless the input clearly says the blocker/churn/offboarding status is resolved.

---

# Cadence Field Logic

Update cadence fields only when a recurring cadence/sync is clearly confirmed.

| Cadence signal | Engagement Status | Outreach Step | Meeting Sync established | Cadence Frequency |
|---|---|---|---|---|
| Weekly sync confirmed | `Cadence established` | `Cadence active` | `yes` | `Weekly` |
| Bi-weekly / every two weeks / fortnightly sync confirmed | `Cadence established` | `Cadence active` | `yes` | `Bi-weekly` |
| Monthly sync confirmed | `Cadence established` | `Cadence active` | `yes` | `Monthly` |
| Quarterly / QBR cadence confirmed | `Cadence established` | `Cadence active` | `yes` | `Quarterly` |
| Ad hoc / as-needed sync confirmed | `Cadence established` | `Cadence active` | `yes` | `Ad hoc / as needed` |
| Recurring cadence confirmed but frequency missing | `Cadence established` | `Cadence active` | `yes` | `TBD / not confirmed` |
| Cadence intentionally paused | `Parked / no active outreach` if no other active work remains | `Parked` | keep current value unless explicitly cancelled | `Paused` |

Rules:

- Do not set Meeting Sync established to `yes` for a one-off meeting.
- Do not set Cadence Frequency unless the frequency is clearly mentioned or cadence is confirmed but missing frequency.
- If the meeting happened but no recurring cadence was agreed, use `Connected - no cadence` and leave cadence fields unchanged.

---

# Confirmation Format

After updating Airtable, always show this structure:

```text
Updated [Account Name] in Airtable with the new activity note and Customer Tasks updates.

Activity Note:
[exact note added, or Already logged — no duplicate appended]

Tasks Updated:
- Created: [task title] — [owner] — [status] — [priority] — [due date or No due date]
- Updated: [task title] — [what changed]
- Done: [task title] — [completion evidence]
- Needs Review: [task title] — [why]
- Still Open: [task title] — [next action]

Current Account State:
- Engagement Status: [current value after update, or unchanged current value]
- Outreach Step: [current value after update, or unchanged current value]
- Meeting Sync established: [current value after update, or Blank]
- Cadence Frequency: [current value after update, or Blank]
- Task status: [current value after task rollup, or Blank]
- Legacy Stage: [current Stage value if available; otherwise Not available]

Status / Cadence Update:
- Engagement Status: [old value → new value, or No change]
- Outreach Step: [old value → new value, or No change]
- Meeting Sync established: [old value → new value, or No change]
- Cadence Frequency: [old value → new value, or No change]
- Task status: [old value → new value, or No change]
```

If no task changes were needed, write:

```text
Tasks Updated:
- No Customer Tasks were created, updated, or closed.
```

Do not omit current workflow/cadence fields. If a value is blank, show `Blank`. If legacy Stage is unavailable, show `Not available`.

---

# What Not To Do

- Do not create a manager recap.
- Do not create a full meeting summary unless the input is clearly a transcript or detailed meeting notes.
- Do not replace existing Activity notes unless explicitly asked.
- Do not invent the account name.
- Do not invent customer activity to justify a status update or task completion.
- Do not update Engagement Status or Outreach Step when the signal is weak or ambiguous.
- Do not omit current Engagement Status, Outreach Step, Meeting Sync established, Cadence Frequency, Task status, or legacy Stage from the confirmation.
- Do not set Meeting Sync established to `yes` unless recurring cadence is confirmed.
- Do not set Cadence Frequency unless clearly supported.
- Do not auto-increment Outreach Step based only on elapsed time.
- Do not update legacy Stage unless explicitly asked.
- Do not close Customer Tasks unless completion is clearly supported.
- Do not create duplicate task rows when an equivalent open task already exists.
- Do not include private internal concerns unless the user clearly says they should be logged.
- Do not make the activity note overly polished or long; it should feel like a practical CRM update.

---

# Final Rule

This skill should always do three things:

1. Append a concise, useful Activity notes entry.
2. Keep Engagement Status, Outreach Step, Meeting Sync established, and Cadence Frequency accurate when the new activity clearly supports a change.
3. Maintain Customer Tasks as the execution tracker by creating, deduping, updating, and closing tasks only when the evidence is clear.

For follow-ups, always perform the follow-up shorthand hard check before confirming the update.