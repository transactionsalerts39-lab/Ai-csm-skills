---
name: airtable-activity-notes-updater
description: >
  Convert a pasted email, one-line note, meeting snippet, or full meeting transcript into
  a clean Airtable account activity note, append it to the correct account in Airtable,
  and update Engagement Status, Outreach Step, Meeting Sync established, and Cadence Frequency
  when the new activity clearly changes the account's relationship, outreach progression,
  or recurring cadence. Use this when the user wants to quickly update Airtable activity
  notes from raw context.
---

# Airtable Activity Notes Updater — Engagement Status Enabled

## Purpose

Create a clean activity-note update from raw input and append it to Airtable.

The user may provide:
- a one-line note
- a rough update
- an email thread
- a customer email
- a meeting snippet
- a full meeting transcript
- notes from a call
- a Slack message or internal update

The skill should turn that input into a concise account update, append it to the account's **Activity notes** field, and update **Engagement Status**, **Outreach Step**, **Meeting Sync established**, and **Cadence Frequency** when the input clearly supports a status or cadence change.

This skill is designed for fast account hygiene. The user should be able to dump raw text and get a clean Airtable activity note without thinking through structure.

---

# Airtable Destination

Use Airtable as the system to update.

Base:
- **Book of Business Management**
- If the user refers to it as **Book of Management**, treat that as the same working base unless Airtable returns multiple matches.
- Base ID: `app6O8peF5ywLe1GM`

Primary table:
- **Accounts**
- Table ID: `tblr6UnvfaqfNvwyU`

Fields to read and update:

| Field | Field ID | Type | Use |
|---|---|---|---|
| Account Name | `fldOSLvopNOX6ae3Z` | singleLineText | Match the customer account |
| Activity notes | `flddz3lqUmEhLhmN5` | multilineText | Append the new activity note |
| Engagement Status | `fldyrxDGOzWF3c7wm` | singleSelect | Current account relationship / engagement state |
| Outreach Step | `fldhX3nTqX4a2eKt8` | singleSelect | Current outreach/follow-up step |
| Meeting Sync established | `fld8kFpch7M4wGfpQ` | singleSelect | Indicates that a recurring customer sync/cadence has been confirmed. Use `yes` only when confirmed. |
| Cadence Frequency | `flddcR78KacwfLyk5` | singleSelect | Confirmed frequency of an established customer cadence. |
| Stage | `fldTYypHjPaFcatCi` | multipleSelects | Legacy fallback only; do not update unless explicitly asked |
| Task status | `fldaYegYsT0eA3NAK` | singleSelect | Supporting task signal |
| Last Activity Date | `fld2jD1HJm9RRwNBW` | lastModifiedTime | Airtable record-touch signal, not proof of customer activity |

Important:
- **Engagement Status** and **Outreach Step** are now the primary workflow fields for account progression.
- **Meeting Sync established** and **Cadence Frequency** are cadence-detail fields. Use them only when a recurring cadence/sync is clearly confirmed or when cadence hygiene needs to be flagged.
- The old **Stage** field is legacy/secondary context. Do not update Stage unless the user explicitly asks.

---

# Engagement Status and Outreach Step Options

## Engagement Status values

Use only these exact values:

- `Transitioned - not contacted`
- `Intro sent - waiting`
- `Customer replied - scheduling`
- `Meeting scheduled`
- `Connected - no cadence`
- `Cadence established`
- `No response - follow-up needed`
- `Multithread required`
- `Support / blocker active`
- `Parked / no active outreach`
- `Churn / offboarding`

## Outreach Step values

Use only these exact values:

- `Not started`
- `Intro sent`
- `FU 1`
- `FU 2`
- `FU 3`
- `FU 4`
- `Multithread`
- `Customer replied`
- `Meeting scheduled`
- `Cadence active`
- `Parked`


## Cadence Frequency values

Use only these exact values:

- `Weekly`
- `Bi-weekly`
- `Monthly`
- `Quarterly`
- `Ad hoc / as needed`
- `Paused`
- `TBD / not confirmed`

---

# Core Behavior

## 1. Identify the account

First, identify the account name from the user's message or pasted content.

If the account name is obvious, proceed.

If the account name is not clear, ask one short clarification question:

```text
Which account should I update this under?
```

Do not guess the account if there are multiple possible accounts.

---

## 2. Identify the input type

Classify the user's input as one of the following.

### A. Simple update

Use this when the input is:
- one line
- a short customer email
- a quick internal note
- a small snippet
- a small status update

Output should be a concise one-sentence activity update by default. Use two sentences only when needed to capture both the customer ask and a clear user-owned next step.

### B. Meeting transcript / meeting notes

Use this when the input is:
- a full transcript
- a long call summary
- detailed meeting notes
- a multi-speaker conversation
- a long customer discussion

Output should include:
- a concise 3–4 sentence meeting summary
- only the top user-owned tasks, if clear follow-up is needed

---

# Date Format

Every Airtable activity note must start with today's date in Asia/Kolkata unless the user explicitly provides another activity date.

Use this format:

```text
DD Month YYYY - [update]
```

Example:

```text
29 April 2026 - Customer confirmed they want to review the workflow setup before launch. Next step is to send a test-run summary and confirm approval to activate.
```

If the user explicitly provides the date of the activity and wants that used, use the provided date instead of today’s date.

If the user does not specify a date, always use today’s date.

---

# Output Format for Airtable

## Simple update format

For simple updates, append this style to Activity notes:

```text
DD Month YYYY - [one-sentence concise update]
```

Example:

```text
29 April 2026 - Alicia shared screenshots showing Ads Media Data Pack discrepancies; main follow-up is to validate account, campaign, Ad ID, date range, and aggregation alignment against 6sense.
```

## Meeting transcript / detailed meeting format

For meeting transcripts or detailed meeting notes, append this style:

```text
DD Month YYYY - [3–4 sentence concise meeting summary]

Tasks identified:
- [ ] [task 1]
- [ ] [task 2]
- [ ] [task 3]
```

Example:

```text
29 April 2026 - Met with the customer to review the audience workflow setup and contact enrichment logic. The customer confirmed they want accounts created only when contacts are available and wants to prioritize senior titles where possible. We reviewed mapping profile requirements and confirmed the core Salesforce fields needed for contact creation. Next step is to prepare a dry run before turning on the full workflow.

Tasks identified:
- [ ] Confirm prioritization logic for seniority and engagement.
- [ ] Prepare duplicated test workflow with dummy segment.
- [ ] Validate account/contact creation behavior before full activation.
```

---

# Engagement Status / Outreach Step Update Logic

After creating the activity note, infer whether **Engagement Status** and/or **Outreach Step** should change.

Only update these fields when the input clearly supports the change. If the signal is ambiguous, leave the current values unchanged and mention that no status change was applied.

## Status mapping

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
| Meeting completed, relationship established, but no recurring sync agreed | `Connected - no cadence` | `Customer replied` |
| Recurring sync / cadence agreed or active | `Cadence established` | `Cadence active` |
| Need to engage additional stakeholders or higher contact | `Multithread required` | `Multithread` |
| Active support issue, blocker, escalation, or unresolved technical issue | `Support / blocker active` | Keep current Outreach Step unless outreach also changed |
| Account intentionally paused / no active outreach planned | `Parked / no active outreach` | `Parked` |
| Churn confirmed, offboarding discussed, or non-renewal confirmed | `Churn / offboarding` | `Parked` |

## Outreach step progression rules

- Do not advance from `FU 1` to `FU 2`, `FU 2` to `FU 3`, or `FU 3` to `FU 4` just because time passed.
- Advance the Outreach Step only when the user’s input clearly says the follow-up was actually sent.
- If the note says a follow-up is needed but not sent, do not advance the step. Keep the current step and make the activity note say the next follow-up is needed.
- If the customer replies, update Outreach Step to `Customer replied` even if the prior value was `FU 1`, `FU 2`, `FU 3`, or `FU 4`.
- If a meeting is scheduled, update Outreach Step to `Meeting scheduled`.
- If a cadence is established, update Outreach Step to `Cadence active`.

## Engagement Status precedence

When multiple signals appear in the same input, use this precedence order:

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

Use the highest-confidence and highest-impact status. Do not overwrite an active support/blocker or churn/offboarding status with a routine outreach status unless the new input clearly says the blocker/churn status is resolved.


## Cadence field update logic

When the input clearly confirms that a recurring customer cadence or sync has been established, update the cadence fields in addition to Engagement Status and Outreach Step.

Use this mapping:

| Cadence signal in input | Engagement Status | Outreach Step | Meeting Sync established | Cadence Frequency |
|---|---|---|---|---|
| Weekly sync / weekly cadence confirmed | `Cadence established` | `Cadence active` | `yes` | `Weekly` |
| Bi-weekly / every two weeks / fortnightly sync confirmed | `Cadence established` | `Cadence active` | `yes` | `Bi-weekly` |
| Monthly sync / monthly cadence confirmed | `Cadence established` | `Cadence active` | `yes` | `Monthly` |
| Quarterly sync / QBR cadence confirmed | `Cadence established` | `Cadence active` | `yes` | `Quarterly` |
| Ad hoc / as-needed sync confirmed | `Cadence established` | `Cadence active` | `yes` | `Ad hoc / as needed` |
| Cadence intentionally paused | `Parked / no active outreach` if no other active work remains | `Parked` | keep current value unless explicitly cancelled | `Paused` |
| Recurring cadence confirmed but frequency not stated | `Cadence established` | `Cadence active` | `yes` | `TBD / not confirmed` |

Rules:

- Do not set **Meeting Sync established** to `yes` for a one-off meeting. A single meeting scheduled is not the same as a recurring cadence.
- Do not set **Cadence Frequency** unless the frequency is clearly mentioned or the cadence is confirmed but the frequency is missing.
- If cadence is confirmed but frequency is missing, set **Cadence Frequency** to `TBD / not confirmed` and mention this in the confirmation output.
- If the user logs a meeting as completed but no recurring cadence was agreed, use `Connected - no cadence` and do not set cadence fields.
- If the input says cadence was cancelled, paused, or no longer active, use `Paused` only when that is explicit.

---

# Updating Airtable

## Update method

Always append to the existing **Activity notes** field.

Do not replace the existing notes unless the user explicitly says:
- replace the note
- overwrite
- clean up the whole field
- rewrite the existing activity notes

Default behavior:
1. Search the Accounts table for the account name.
2. Confirm the returned account strongly matches the requested account.
3. Pull the current values for:
   - Activity notes
   - Engagement Status
   - Outreach Step
   - Meeting Sync established
   - Cadence Frequency
   - Stage as legacy fallback only
4. Create the new activity note using the rules above.
5. Append the new note to the bottom of existing Activity notes with two line breaks.
6. Infer new Engagement Status and Outreach Step if the input clearly supports a change.
7. Update the Airtable record with:
   - appended Activity notes
   - Engagement Status, only if a clear change is needed
   - Outreach Step, only if a clear change is needed
   - Meeting Sync established, only if a recurring cadence is clearly confirmed
   - Cadence Frequency, only if clearly supported or cadence is confirmed without frequency
8. Confirm the update and always show the current account workflow state after the update:
   - Engagement Status
   - Outreach Step
   - Meeting Sync established
   - Cadence Frequency
   - Legacy Stage
   - Whether each status/cadence field changed or did not change

For single select fields, write the plain option name, not the returned object.

---

# Airtable Workflow

1. Search the **Accounts** table for the account name.
2. Confirm the returned account strongly matches the requested account.
3. Pull current values for:
   - Account Name
   - Activity notes
   - Engagement Status
   - Outreach Step
   - Meeting Sync established
   - Cadence Frequency
   - Stage
   - Task status, if useful
4. Create the new activity note.
5. Determine if Engagement Status and Outreach Step should change.
6. Update the record.
7. Confirm exactly what was updated and always show the current Engagement Status, Outreach Step, Meeting Sync established, Cadence Frequency, and legacy Stage after the update.

If multiple accounts match:
- Show the likely matches.
- Ask the user which account to update.

If no account matches:
- Tell the user no matching Airtable account was found.
- Ask whether they want to provide the exact account name.

---

# Confirmation Message

After updating Airtable, respond briefly.

Every output must explicitly show the account's current workflow and cadence state, even when Engagement Status, Outreach Step, Meeting Sync established, or Cadence Frequency did not change.

Do not only say that no status update was applied. Always show the current workflow and cadence values after the update.

## Required confirmation format

Use this format every time:

```text
Updated [Account Name] in Airtable with the new activity note.

Activity Note Added:
[exact note added]

Current Account State:
- Engagement Status: [current value after update, or unchanged current value]
- Outreach Step: [current value after update, or unchanged current value]
- Meeting Sync established: [current value after update, or Blank]
- Cadence Frequency: [current value after update, or Blank]
- Legacy Stage: [current Stage value if available; otherwise Not available]

Status / Cadence Update:
- Engagement Status: [old value → new value, or No change]
- Outreach Step: [old value → new value, or No change]
- Meeting Sync established: [old value → new value, or No change]
- Cadence Frequency: [old value → new value, or No change]
```

If only the activity note changed, still show the current account state:

```text
Updated [Account Name] in Airtable with the new activity note.

Activity Note Added:
26 June 2026 - Sent an introductory email to Catherine and the AP team at Uptempo; next step is to wait for a reply and follow up if there is no response.

Current Account State:
- Engagement Status: Intro sent - waiting
- Outreach Step: Intro sent
- Meeting Sync established: Blank
- Cadence Frequency: Blank
- Legacy Stage: to reach out

Status / Cadence Update:
- Engagement Status: No change
- Outreach Step: No change
- Meeting Sync established: No change
- Cadence Frequency: No change
```

If Engagement Status or Outreach Step changed, show both the new current state and the change:

```text
Updated [Account Name] in Airtable with the new activity note.

Activity Note Added:
26 June 2026 - Salt Security replied and asked for meeting times next week; next step is to send availability and confirm the meeting slot.

Current Account State:
- Engagement Status: Customer replied - scheduling
- Outreach Step: Customer replied
- Meeting Sync established: Blank
- Cadence Frequency: Blank
- Legacy Stage: Email response pending

Status / Cadence Update:
- Engagement Status: Intro sent - waiting → Customer replied - scheduling
- Outreach Step: Intro sent → Customer replied
- Meeting Sync established: No change
- Cadence Frequency: No change
```

If only one status or cadence field changed, show `No change` for the other fields.

If a current value is blank, show `Blank` rather than omitting the line.

If the legacy Stage field is unavailable, show `Not available` rather than omitting the line.

Do not omit the Current Account State or Status / Cadence Update sections.

Do not create a long explanation after the confirmation.

---

# Writing Rules

## Conciseness Budget

Default to the shortest useful activity note.

Simple updates:
- Default to one sentence.
- Target 25–45 words.
- Maximum 60 words unless critical details require more.
- Use two sentences only when needed to capture both the customer ask and the user-owned next step.

Meeting transcripts / detailed notes:
- Target 80–120 words total.
- Maximum 150 words including tasks.
- Use 3–4 sentences, not a full recap.
- Include only the top user-owned tasks, maximum 3.
- Omit the task section if there are no clear user-owned follow-ups.

When shortening, preserve customer asks, decisions, blockers, next steps, owners, dates, numbers, deadlines, ticket IDs, and product names. Cut background, repeated context, and general discussion details first.

## Compression Priority

When compressing, preserve in this order:
1. Customer ask or decision
2. Blocker or risk
3. Next step / owner
4. Critical dates, numbers, tickets, or product names

Cut background and nice-to-have context first.

## Preserve important details

Always preserve:
- customer names
- product names
- support ticket references
- technical terms
- dates
- numbers
- deadlines
- renewal dates
- meeting dates
- owners
- explicit customer asks

Do not invent missing details.

---

# Task Extraction Rules

For meeting transcripts or detailed notes, extract tasks owned by the user.

Look for phrases such as:
- I will
- I’ll
- Ranjodh to
- need to
- follow up
- send
- schedule
- confirm
- check
- raise a ticket
- loop in
- prepare
- review
- share
- get back to them
- coordinate
- track support
- update Airtable

Do not list tasks owned only by the customer unless they affect the user’s follow-up.

Only include tasks that require a clear follow-up from the user. Limit to the top 3 tasks. If there are no clear user-owned tasks, omit the Tasks identified section.

If a task owner is unclear but it sounds like the CSM owns it, include it.

---

# Status Update Examples

## Example 1 — intro sent

User input:

```text
Uptempo - sent intro email to Catherine and AP team, waiting for reply.
```

Airtable note:

```text
26 June 2026 - Sent an introductory email to Catherine and the AP team at Uptempo; next step is to wait for a reply and follow up if there is no response.
```

Status update:

```text
Engagement Status = Intro sent - waiting
Outreach Step = Intro sent
```

## Example 2 — customer replied

User input:

```text
Salt Security replied and asked for times next week. Need to send availability.
```

Airtable note:

```text
26 June 2026 - Salt Security replied and asked for meeting times next week; next step is to send availability and confirm the meeting slot.
```

Status update:

```text
Engagement Status = Customer replied - scheduling
Outreach Step = Customer replied
```

## Example 3 — support blocker

User input:

```text
Analytic Partners is blocked on the Ads Media Data Pack discrepancy. I need to follow up with Support.
```

Airtable note:

```text
26 June 2026 - Analytic Partners remains blocked on the Ads Media Data Pack discrepancy; next step is to follow up with Support and share the update with the customer.
```

Status update:

```text
Engagement Status = Support / blocker active
Outreach Step = keep current value unless outreach also changed
```


## Example 4 — cadence established with frequency

User input:

```text
Boostlingo - monthly cadence established with Sree and customer team.
```

Airtable note:

```text
26 June 2026 - Monthly cadence was established with Sree and the Boostlingo customer team; next step is to keep the recurring sync active and track follow-ups through the account plan.
```

Status and cadence update:

```text
Engagement Status = Cadence established
Outreach Step = Cadence active
Meeting Sync established = yes
Cadence Frequency = Monthly
```

## Example 5 — cadence established but frequency missing

User input:

```text
Graylog confirmed they want a recurring sync going forward.
```

Airtable note:

```text
26 June 2026 - Graylog confirmed they want a recurring sync going forward; next step is to confirm the cadence frequency and schedule the recurring meeting series.
```

Status and cadence update:

```text
Engagement Status = Cadence established
Outreach Step = Cadence active
Meeting Sync established = yes
Cadence Frequency = TBD / not confirmed
```

---

# What Not To Do

- Do not create a manager recap.
- Do not create a full meeting summary unless the input is clearly a transcript or detailed meeting notes.
- Do not replace existing Airtable notes unless explicitly asked.
- Do not invent the account name.
- Do not invent customer activity to justify a status update.
- Do not update Engagement Status or Outreach Step when the signal is weak or ambiguous.
- Do not omit the current Engagement Status, Outreach Step, Meeting Sync established, Cadence Frequency, or legacy Stage from the confirmation output.
- Do not set Meeting Sync established to `yes` unless a recurring sync/cadence is clearly confirmed.
- Do not set Cadence Frequency unless the frequency is clearly mentioned or cadence is confirmed but frequency is missing.
- Do not auto-increment Outreach Step based only on elapsed time.
- Do not downgrade support/blocker or churn/offboarding statuses unless the input clearly says the blocker or churn/offboarding motion is resolved.
- Do not update the legacy Stage field unless the user explicitly asks.
- Do not include private internal concerns unless the user’s input clearly says they should be logged.
- Do not make the activity note overly polished or long; it should feel like a practical CRM/Airtable update.
- Omit background, repeated context, relationship commentary, and process details unless they change the account status or next step.

---

# Final Rule

This skill should update the account record in two ways:

1. Append a concise, useful Activity notes entry.
2. Keep Engagement Status and Outreach Step current when the new activity clearly shows account progression.
3. Keep Meeting Sync established and Cadence Frequency current when the new activity clearly confirms a recurring cadence.

Speed and accuracy matter more than over-documenting. If the status change is clear, update it. If it is not clear, leave the status fields alone and only append the note.
