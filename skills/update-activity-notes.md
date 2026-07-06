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

Create a concise Airtable activity note from raw customer/internal context, append it to the correct account, and keep account workflow fields accurate.

This skill handles:
- one-line updates
- rough notes
- email threads
- customer emails
- meeting snippets
- full meeting transcripts
- Slack/internal updates
- follow-up emails/messages that should be logged

The skill must prioritize speed, accuracy, and workflow hygiene. Do not over-document.

---

# Source of Truth and Destination

Use Airtable as the system to update.

Base:
- Book of Business Management
- Base ID: `app6O8peF5ywLe1GM`

Primary table:
- Accounts
- Table ID: `tblr6UnvfaqfNvwyU`

Fields to read and update:

| Field | Field ID | Use |
|---|---|---|
| Account Name | `fldOSLvopNOX6ae3Z` | Match the customer account |
| Activity notes | `flddz3lqUmEhLhmN5` | Append the new activity note |
| Engagement Status | `fldyrxDGOzWF3c7wm` | Primary account relationship / engagement state |
| Outreach Step | `fldhX3nTqX4a2eKt8` | Primary outreach/follow-up sequence state |
| Meeting Sync established | `fld8kFpch7M4wGfpQ` | Use `yes` only when a recurring sync/cadence is confirmed |
| Cadence Frequency | `flddcR78KacwfLyk5` | Confirmed cadence frequency |
| Stage | `fldTYypHjPaFcatCi` | Legacy fallback only; do not update unless explicitly asked |
| Task status | `fldaYegYsT0eA3NAK` | Supporting task signal |
| Last Activity Date | `fld2jD1HJm9RRwNBW` | Airtable record-touch signal, not proof of customer activity |

Important:
- Engagement Status and Outreach Step are the primary workflow fields.
- Stage is legacy context only and must not be updated unless the user explicitly asks.
- For single select fields, write the plain option name, not the object returned by Airtable.

---

# Allowed Field Values

## Engagement Status

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

## Outreach Step

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

## Cadence Frequency

Use only these exact values:
- `Weekly`
- `Bi-weekly`
- `Monthly`
- `Quarterly`
- `Ad hoc / as needed`
- `Paused`
- `TBD / not confirmed`

---

# Core Workflow

1. Identify the account from the user message or pasted content.
   - If obvious, proceed.
   - If missing or ambiguous, ask: `Which account should I update this under?`
2. Fetch the account from Airtable and verify the returned Account Name strongly matches.
3. Pull current values for Activity notes, Engagement Status, Outreach Step, Meeting Sync established, Cadence Frequency, and legacy Stage.
4. Classify the activity signal.
5. Create the new activity note.
6. Determine status/cadence changes, including the follow-up hard check below.
7. Append the note to Activity notes with two line breaks.
8. Update the Airtable record.
9. Confirm exactly what changed and show the full current workflow/cadence state.

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

Do not advance outreach steps just because time passed. Advance only when the user input clearly says a follow-up was actually sent or the workflow includes both a follow-up draft and an explicit `/update notes` request.

## Follow-up shorthand hard check

When the user asks to draft, make, write, or send a follow-up email/message and also asks to update notes in the same request, treat the activity as a **sent follow-up** unless the user explicitly says:
- draft only
- for review only
- not sent
- not ready to log
- do not update/send yet

Before writing Airtable for any follow-up activity:
1. Read the current Outreach Step.
2. Compare the new activity signal against the current Outreach Step.
3. If the new activity is a sent follow-up, advance exactly one follow-up step:
   - `Intro sent`, `Not started`, or blank after a prior intro exists → `FU 1`
   - `FU 1` → `FU 2`
   - `FU 2` → `FU 3`
   - `FU 3` → `FU 4`
   - `FU 4` → keep `FU 4`; consider recommending multithread or park, but do not auto-change without evidence
4. Keep Engagement Status as `No response - follow-up needed` for no-response follow-up steps unless a higher-priority signal exists.
5. In the final confirmation, explicitly show the Outreach Step transition.
6. Never report `No change` for Outreach Step when a sent follow-up caused the step to advance.

If the user only asks for wording and does not ask to update Airtable, do not log the activity as sent.

Example:

Current Airtable state:
```text
Engagement Status = No response - follow-up needed
Outreach Step = FU 1
```

User request:
```text
make a fu email, then /update notes : Sponsor United
```

Correct result:
```text
Engagement Status = No response - follow-up needed
Outreach Step = FU 2
```

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

Do not omit current workflow/cadence fields. If a value is blank, show `Blank`. If legacy Stage is unavailable, show `Not available`.

---

# What Not To Do

- Do not create a manager recap.
- Do not create a full meeting summary unless the input is clearly a transcript or detailed meeting notes.
- Do not replace existing Activity notes unless explicitly asked.
- Do not invent the account name.
- Do not invent customer activity to justify a status update.
- Do not update Engagement Status or Outreach Step when the signal is weak or ambiguous.
- Do not omit current Engagement Status, Outreach Step, Meeting Sync established, Cadence Frequency, or legacy Stage from the confirmation.
- Do not set Meeting Sync established to `yes` unless recurring cadence is confirmed.
- Do not set Cadence Frequency unless clearly supported.
- Do not auto-increment Outreach Step based only on elapsed time.
- Do not update legacy Stage unless explicitly asked.
- Do not include private internal concerns unless the user clearly says they should be logged.
- Do not make the activity note overly polished or long; it should feel like a practical CRM update.

---

# Final Rule

This skill should always do two things:

1. Append a concise, useful Activity notes entry.
2. Keep Engagement Status, Outreach Step, Meeting Sync established, and Cadence Frequency accurate when the new activity clearly supports a change.

For follow-ups, always perform the follow-up shorthand hard check before confirming the update.
