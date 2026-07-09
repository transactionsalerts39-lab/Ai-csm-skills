---
name: meeting-summarizer
description: >
  Convert customer meeting transcripts, rough notes, call summaries, or conversation dumps
  into a concise CRM activity note, a structured Airtable Meeting Notes record, a warm
  customer follow-up email, updated Airtable account workflow fields, and durable Customer
  Tasks records when clear next steps are created, updated, or completed.
---

# Meeting Summarizer — Task Lifecycle V3

## Purpose

Turn meeting notes, transcripts, or rough customer-call context into the outputs Ranjodh needs for account follow-up:

1. A concise CRM-style activity note appended to **Accounts** → **Activity notes**.
2. A structured **Meeting Notes** record created in **Detailed Notes**.
3. A warm customer-facing follow-up email rendered as an editable writing block.
4. Accurate **Engagement Status**, **Outreach Step**, **Meeting Sync established**, and **Cadence Frequency** updates when the meeting clearly changes account progression.
5. Durable **Customer Tasks** records for clear next steps, customer-waiting items, support/blocker follow-ups, and task completion evidence.

The chat output should stay clean. Do not output raw task rows by default unless the user asks for tasks, task records, or check-off details.

---

## Airtable Source of Truth

Base:

- Book of Business Management
- Base ID: `app6O8peF5ywLe1GM`

Accounts table:

- Table ID: `tblr6UnvfaqfNvwyU`

Detailed Notes table:

- Table ID: `tblI5cCnIY63S6pZq`

Customer Tasks table:

- Table ID: `tblWUzwjGM4pwhAqR`

Always use `schema/airtable-schema-map.md` for current field IDs and allowed values.

### Accounts fields

| Field | Field ID | Use |
|---|---|---|
| Account Name | `fldOSLvopNOX6ae3Z` | Match account |
| Activity notes | `flddz3lqUmEhLhmN5` | Append CRM note |
| Engagement Status | `fldyrxDGOzWF3c7wm` | Account relationship state |
| Outreach Step | `fldhX3nTqX4a2eKt8` | Outreach/follow-up progression |
| Meeting Sync established | `fld8kFpch7M4wGfpQ` | `yes` only when recurring cadence is confirmed |
| Cadence Frequency | `flddcR78KacwfLyk5` | Confirmed cadence frequency |
| Task status | `fldaYegYsT0eA3NAK` | Account-level rollup from Customer Tasks |
| Stage | `fldTYypHjPaFcatCi` | Legacy fallback only; do not update unless explicitly asked |

### Detailed Notes fields

| Field | Field ID | Value |
|---|---|---|
| Account | `fldkJTerhbTfcObzR` | Linked matched account record |
| Activity Type | `fldfi0aH7CncdNtb9` | `Meeting Notes` |
| Date | `fld5Dd0gO8vHadVjl` | Meeting date |
| Notes | `fldcfsEsMgFHoB8VH` | Structured meeting notes |
| Title | `fldbf738tn5U18z7D` | `Meeting Notes - [Account Name] - [Date]` when useful |
| Next Steps | `flduD4nsj6ZkEXmLZ` | Clear next steps only |
| Customer Tasks | `fld5rGBShRUYrYIiR` | Link created/updated Customer Tasks when supported |

### Customer Tasks fields

| Field | Field ID | Use |
|---|---|---|
| Task Title | `fldbPKh3KNG9vbeXS` | Short action-oriented task name |
| Account | `fldMIaXj2PtqHkmNk` | Linked account record |
| Owner | `fldVF6TQNNaPmv8qf` | `Ranjodh`, `Customer`, `AE / AM`, `Support`, `Internal Team`, `Shared`, `Unknown` |
| Status | `fldFbZYJvZUZmrGf2` | Current task state |
| Priority | `fldGixN6MmFNAbTdG` | `P1 - Must do`, `P2 - Should do`, `P3 - Monitor` |
| Due Date | `fldm4uWSm23HVxZPC` | Due date when clear |
| Customer Waiting? | `fldDXgxychawzNCZn` | Checked when customer is waiting on Ranjodh/6sense/internal owner |
| Source Type | `fldRaStx1IoK4ooqv` | Use `Meeting` for meeting notes, `Transcript` for raw transcript-only source |
| Source Date | `fldp9gpKEdSIywADe` | Meeting/source date |
| Source Summary | `fldKEtfu2JfA5VTKK` | Why the task exists |
| Source Detail Note | `fldo4zvmVhOXqed2p` | Link to the created Detailed Notes record when available |
| Related Email / Meeting Summary | `fldhj5zbRG2dwT2sF` | Relevant excerpt or condensed source context |
| Completion Evidence | `fldsWVTfAAagVBTPT` | Evidence used when marking done |
| Completed Date | `fldcOYlYd9dfmyl5K` | Completion date when supported |
| Needs Review | `fld5SXTsSrQNl96t2` | Check when matching/completion is uncertain |
| Last Updated From | `fldg0rM7UT6Jt148a` | Always write `Meeting Summarizer` from this skill |

---

## Default Chat Output

By default, show only these sections in this order:

```md
### CRM Activity Note
[copy-paste-ready note]

### Airtable Meeting Notes Record
- Account: [Account Name]
- Activity Type: Meeting Notes
- Date: [Meeting Date]
- Notes: [structured meeting notes]

### Customer Follow-Up Email
:::writing{variant="email" id="[unique 5-digit id]" subject="[subject]"}
[email body]
:::

[one-line Airtable update confirmation]
```

Do not output standalone task records by default. Mention task changes only in the one-line confirmation unless the user asks for tasks/check-off details.

If the user asks for only one section, output only that section and still perform Airtable updates if the request clearly implies update behavior.

---

## Workflow

### Step 1 — Identify account and meeting date

Extract:

- Account name
- Meeting date
- Customer participants, if obvious
- Internal participants, if obvious
- Main discussion topics
- Customer asks
- Agreed next steps
- Meeting outcome signals that affect account workflow or Customer Tasks

If account name is missing, ask:

```text
Which account should I update this under?
```

If meeting date is missing, use today's date in Asia/Kolkata and proceed.

### Step 2 — Pull Airtable context

1. Search Accounts by Account Name.
2. Confirm strong account match.
3. Pull current Accounts values for Activity notes, workflow/cadence fields, Task status, and legacy Stage.
4. Pull existing open/actionable Customer Tasks for the account:
   - `Open`
   - `In Progress`
   - `Waiting on Internal Team`
   - `Waiting on Customer`
   - `Blocked`
   - `Needs Review`
5. Exclude `Done` and `Cancelled` unless the user explicitly asks for closed history.

### Step 3 — Create CRM activity note

Format:

```text
DD Month YYYY - [2–4 sentence CRM activity note]
```

Rules:

- Capture what was discussed, what changed, and the most important next step.
- Include product names, blockers, dates, numbers, owners, and technical terms exactly when provided.
- Mention sentiment/risk only when clearly discussed or strongly evident.
- Do not include internal-only strategy or speculation.

### Step 4 — Create Detailed Notes record

Create one Detailed Notes record with Activity Type = `Meeting Notes`.

The Notes field should include only sections with real content:

```text
Meeting Overview
- Account: [Account Name]
- Meeting date: [Meeting Date]
- Meeting purpose: [short purpose]
- Participants: [participants if known]

Executive Summary
[2–4 sentence summary]

Customer Stated Priorities
- [priority / goal / success criteria]

Key Discussion / Decisions
- [important topic or decision]

Risks / Blockers / Signals
- [only if real]

What I Should Do Next as the CSM
- [CSM-owned next step]

Open Questions / Needs Confirmation
- [only if real]
```

Populate Detailed Notes → Next Steps only when next steps are clearly stated and useful.

### Step 5 — Maintain Customer Tasks

Create, update, dedupe, or close Customer Tasks based on the meeting.

#### What counts as a task

Create or update a Customer Task only when there is a concrete action and a reasonably clear owner.

Good task examples:

- Send resources or documentation.
- Share follow-up notes, deck, recording, report, or link.
- Confirm product behavior or documentation.
- Check internally with Support, Product, Engineering, AE/AM, or Ops.
- Raise or track a support case.
- Schedule/send invite for a follow-up meeting.
- Prepare reporting, workflow recommendation, success plan, QBR/EBR, or renewal next step.
- Confirm procurement, pricing, renewal, legal, or stakeholder path.

Do not create task rows for vague relationship goals like `keep momentum`, `continue partnership`, or `monitor account` unless a concrete next action exists.

#### Owner mapping

| Signal | Owner |
|---|---|
| Ranjodh / CSM / 6sense CSM owes the action | `Ranjodh` |
| Customer owes the action | `Customer` |
| AE / AM / commercial owner owes the action | `AE / AM` |
| Support owns investigation or resolution | `Support` |
| Product, Engineering, Ops, or unspecified 6sense team owns it | `Internal Team` |
| Both customer and 6sense need to act | `Shared` |
| Owner is unclear but action is real | `Unknown` and Needs Review checked |

#### Status mapping

| Meeting signal | Task Status |
|---|---|
| New action owed by Ranjodh / 6sense | `Open` |
| Work has started but is not complete | `In Progress` |
| Waiting on Support / internal team | `Waiting on Internal Team` |
| Waiting on customer input / availability | `Waiting on Customer` |
| Blocker prevents progress | `Blocked` |
| Meeting confirms a task was completed | `Done` |
| Task no longer needed | `Cancelled` |
| Ambiguous task, duplicate, owner, or completion | `Needs Review` |

#### Priority mapping

- `P1 - Must do` when the customer is waiting, the item is a blocker/support escalation, renewal-critical, executive request, or due within 7 days.
- `P2 - Should do` for normal follow-ups, resources, scheduling, and coordination.
- `P3 - Monitor` for low-urgency tracking items that still need visibility.

#### Customer Waiting? checkbox

Check `Customer Waiting?` when the customer is waiting on Ranjodh, Support, AE/AM, or another internal 6sense owner.

Usually do not check it for purely customer-owned work unless Ranjodh still needs to track or nudge it.

### Step 6 — Deduplicate tasks

Before creating a new Customer Task, search active Customer Tasks for the same account.

Dedupe key:

```text
Account + normalized task/action + owner + active status
```

If a similar open task already exists:

- Update the existing task with new due date, priority, source summary, customer-waiting flag, or detail-note link.
- Do not create a duplicate.

If multiple possible matches exist:

- Do not merge automatically.
- Mark the relevant task as `Needs Review` and check Needs Review.
- Mention this in the confirmation only if the user asked for task details.

### Step 7 — Check off completed tasks

Compare the meeting content against existing active Customer Tasks.

Mark a task `Done` only when all are true:

1. Same account.
2. Existing task is active.
3. Meeting clearly proves the specific task was completed.
4. Completion evidence can be quoted or summarized.

Completion signals include:

- sent
- shared
- attached
- confirmed
- created / raised support case
- looped in
- scheduled
- completed
- resolved
- followed up
- provided update

When marking complete, update:

- Status = `Done`
- Completed Date = meeting date or today's date in Asia/Kolkata
- Completion Evidence = exact or concise evidence
- Needs Review = unchecked unless ambiguity remains
- Last Updated From = `Meeting Summarizer`

Do not close tasks from vague language like `will send`, `planning to`, `checking internally`, or `should have an update soon`.

### Step 8 — Update account workflow fields

Update Engagement Status / Outreach Step only when clearly supported.

| Meeting signal | Engagement Status | Outreach Step |
|---|---|---|
| Meeting scheduled but not happened | `Meeting scheduled` | `Meeting scheduled` |
| Meeting happened, customer engaged, no cadence confirmed | `Connected - no cadence` | `Customer replied` |
| Recurring sync / cadence agreed | `Cadence established` | `Cadence active` |
| Customer replied but meeting not booked | `Customer replied - scheduling` | `Customer replied` |
| Unresolved support issue / blocker | `Support / blocker active` | Keep current unless outreach also changed |
| Additional stakeholders needed | `Multithread required` | `Multithread` |
| Churn/offboarding/non-renewal confirmed | `Churn / offboarding` | `Parked` |
| No active outreach needed | `Parked / no active outreach` | `Parked` |

Cadence update mapping:

| Meeting outcome | Engagement Status | Outreach Step | Meeting Sync established | Cadence Frequency |
|---|---|---|---|---|
| Weekly sync agreed | `Cadence established` | `Cadence active` | `yes` | `Weekly` |
| Bi-weekly sync agreed | `Cadence established` | `Cadence active` | `yes` | `Bi-weekly` |
| Monthly sync agreed | `Cadence established` | `Cadence active` | `yes` | `Monthly` |
| Quarterly/QBR cadence agreed | `Cadence established` | `Cadence active` | `yes` | `Quarterly` |
| Ad hoc/as-needed cadence agreed | `Cadence established` | `Cadence active` | `yes` | `Ad hoc / as needed` |
| Recurring cadence agreed but frequency missing | `Cadence established` | `Cadence active` | `yes` | `TBD / not confirmed` |

Do not set Meeting Sync established to `yes` for a one-off meeting.

### Step 9 — Recalculate account Task status

After Customer Tasks changes, update Accounts → Task status only as a summary signal:

- If one or more linked Customer Tasks are active → `Open`.
- If one or more linked Customer Tasks exist but none has started and Airtable has the option → `yet to start`.
- If no linked Customer Tasks remain active and Airtable has the option → `closed`.
- Do not force-create select options.

---

## Customer Follow-Up Email

Always draft a customer-facing follow-up email from the meeting context unless the user says not to.

Use `variant="email"`, include the subject in writing-block metadata, and use a unique 5-digit writing-block `id`.

The email must be:

- Warm
- Human
- Customer-safe
- Concise
- Clear on important next steps
- Free of internal-only risk, strategy, or negative commentary

Do not make the email feel like a project tracker unless the meeting was highly tactical.

---

## Final Chat Output Format

Use this exact order by default:

````md
### CRM Activity Note
```text
[DD Month YYYY - CRM note]
```

### Airtable Meeting Notes Record
```text
Account: [Account Name]
Activity Type: Meeting Notes
Date: [YYYY-MM-DD or DD Month YYYY]
Notes:
[approved structured notes]
```

### Customer Follow-Up Email

:::writing{variant="email" id="[unique 5-digit id]" subject="[Subject]"}
Hi [Customer Name],

[email body]

Best,
Ranjodh
:::

[One-line Airtable update confirmation. Mention task changes only in summary form unless the user asked for details.]
````

Confirmation examples:

```text
Updated [Account] in Airtable with the CRM activity note, Meeting Notes record, 2 Customer Tasks created, 1 task closed, and status/cadence fields where supported.
```

```text
Updated [Account] in Airtable with the CRM activity note and Meeting Notes record. No Customer Tasks or status/cadence changes were needed.
```

If the user asks for task detail, include:

```text
Customer Tasks Updated:
- Created: [task] — [owner] — [status] — [priority] — [due date or No due date]
- Updated: [task] — [what changed]
- Done: [task] — [completion evidence]
- Needs Review: [task] — [why]
- Still Open: [task] — [next action]
```

---

## Narrow Request Rules

- “Only CRM note” → output only CRM Activity Note; update Airtable if implied.
- “Only Airtable note” → output only Airtable Meeting Notes Record.
- “Only email” → output only Customer Follow-Up Email.
- “Update Airtable only” → update Airtable and provide one short confirmation.
- “No email” → skip email.
- “Notes only” / “do not update statuses” → do not update Engagement Status or Outreach Step, but still create/update Customer Tasks if the user asked to update Airtable and tasks are clear unless they also say no task updates.
- “Only tasks” → output only Customer Tasks created/updated/closed and update Airtable.

---

## Edge Cases

### Account not found

Do not guess. Provide copy-paste outputs and say:

```text
I could not find a matching Airtable account for [Account Name], so I did not update Airtable.
```

### Multiple account matches

Ask one short clarification question and do not update Airtable until the user confirms.

### Support issue discussed

Do not create a support ticket unless the user explicitly asks. If the issue is active/unresolved, create or update a Customer Task owned by `Support` or `Internal Team`, set Customer Waiting? when the customer expects an update, and update Engagement Status to `Support / blocker active` when supported.

### No clear next steps

Do not create task records. Still create the CRM note and Detailed Notes record.

### Ambiguous completion

Do not mark the task Done. Set Needs Review if the task needs attention.

---

## Quality Checklist

Before finalizing, verify:

- Account is identified and strongly matched.
- CRM note is short and factual.
- Detailed Notes record uses Activity Type = `Meeting Notes`.
- Customer follow-up email is in an editable writing block.
- Customer Tasks were created only for concrete actions.
- Existing open Customer Tasks were checked for dedupe and completion evidence.
- Account Task status rollup was updated when task rows changed.
- Engagement Status / Outreach Step were updated only when supported.
- Meeting Sync established / Cadence Frequency were updated only for confirmed recurring cadence.
- Legacy Stage was not updated unless explicitly asked.

---

## Final Rule

This skill should give Ranjodh a clean CRM activity update, a structured Airtable Meeting Notes record, a customer-safe follow-up email, accurate account workflow/cadence updates, and durable Customer Tasks for concrete work that needs to be done or checked off. Activity notes are the history layer; Detailed Notes are the meeting detail layer; Customer Tasks is the execution tracker.
