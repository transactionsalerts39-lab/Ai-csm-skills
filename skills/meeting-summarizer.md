---
name: meeting-summarizer
description: >
  Convert customer meeting transcripts, rough notes, call summaries, or conversation dumps
  into a concise CRM activity note, a structured Airtable Meeting Notes record, a warm
  customer follow-up email, and updated Airtable Engagement Status / Outreach Step / cadence fields
  when the meeting clearly changes the account relationship, outreach progression, or recurring cadence.
  Use this skill when the user asks to summarize a meeting, create meeting notes,
  create a CRM/Salesforce note from a call, update Airtable after a customer meeting,
  or draft a customer follow-up email from meeting context.
---

# Meeting Summarizer — CRM Activity Note + Airtable Meeting Notes + Follow-Up Email + Engagement Status

## Purpose

Turn meeting notes, transcripts, or rough customer-call context into only the outputs Ranjodh needs for account follow-up:

1. A concise CRM-style activity note appended to the **Accounts** table → **Activity notes** field.
2. A structured **Meeting Notes** record created in the **Activity/Notes** table.
3. A warm customer-facing follow-up email based on the meeting, delivered as an editable writing block.
4. Updated **Engagement Status**, **Outreach Step**, **Meeting Sync established**, and **Cadence Frequency** on the Accounts table when the meeting clearly changes account progression or confirms a recurring cadence.

This skill replaces the longer meeting-summary workflow. Do **not** generate the old 11-section meeting summary in the chat. Do **not** generate Slack updates, task records, raw risk tables, internal strategy sections, or a long debrief unless the user explicitly asks for those separately.

The only place where selected meeting-summary sections should appear is inside the **Activity/Notes** table → **Notes** field, because that field needs the important meeting context stored cleanly.

---

## Core Output Rule

By default, show only these sections in the chat:

```md
### CRM Activity Note
[copy-paste-ready note]

### Airtable Meeting Notes Record
- Account: [Account Name]
- Activity Type: Meeting Notes
- Date: [Meeting Date]
- Notes: [structured meeting notes for the Activity/Notes table]

### Customer Follow-Up Email
:::writing{variant="email" id="[unique 5-digit id]" subject="[subject]"}
[email body]
:::
```

Email output rule:
- The customer follow-up email must always be rendered in an editable writing block, not a plain markdown code block.
- Use `variant="email"`, include the subject in writing-block metadata, and use a unique random 5-digit `id`.
- Do not put `Subject:` inside the email body because the subject belongs in writing-block metadata.

If Airtable is updated successfully, add one short confirmation after the output:

```md
Updated [Account Name] in Airtable with the CRM activity note, Meeting Notes record, and Engagement Status / Outreach Step where applicable.
```

If Airtable cannot be updated, say why in one sentence and still provide the copy-paste-ready sections above.

---

## Sections to Store Inside the Activity/Notes → Notes Field

Use only the important sections from the prior 11-section meeting-summary workflow. These sections should be stored inside the **Notes** field of the Activity/Notes record.

Include these sections:

1. **Meeting Overview**
   - Short context only: meeting purpose, account, participants if known, and date.

2. **Executive Summary**
   - 2–4 sentences summarizing the meeting outcome, customer sentiment, and why the discussion matters.

3. **Customer Stated Priorities**
   - Customer goals, business priorities, product priorities, deadlines, success criteria, or desired outcomes.

4. **Key Discussion / Decisions**
   - The most important topics discussed, decisions made, workflow/product items reviewed, and any confirmed alignment.
   - This replaces the long “Detailed Internal Summary.” Keep it concise and useful.

5. **Risks / Blockers / Signals**
   - Include only if risks, blockers, churn signals, technical blockers, commercial concerns, adoption friction, stakeholder changes, or urgency were actually discussed or clearly surfaced.
   - Do not invent risks.
   - Do not include internal-only strategy or commentary that should not live in an account activity record.

6. **What I Should Do Next as the CSM**
   - CSM-owned next steps only.
   - Include customer-owned next steps only if they are needed to understand the CSM follow-up.

7. **Open Questions / Needs Confirmation**
   - Include only if there are unresolved items, assumptions, or details that need to be confirmed.

Do **not** include these old sections inside the Activity/Notes → Notes field:

- Salesforce / CRM Note
- Slack update to internal team
- Customer follow-up email
- Very short version
- Raw transcript-style summary
- Long detailed internal summary
- Hidden internal strategy

The CRM note belongs in the **Accounts** table → **Activity notes** field. The customer follow-up email belongs in the chat output only, unless the user asks to store it somewhere.

---

## What Not To Output in Chat

Do **not** output the full old 11-section summary in the chat unless the user explicitly asks for it.

Do **not** output any of the following as separate chat sections by default:

- Full meeting overview table
- Full detailed internal summary
- Standalone risks and signals section
- Standalone CSM action plan section
- Standalone open questions section
- Slack update
- Very short version
- Raw transcript cleanup
- Separate task records
- Legacy Stage field update
- Long internal commentary

The user wants the output to stay clean, readable, and immediately usable.

---

# Airtable Source of Truth

Use Airtable as the working system for account notes and account workflow status.

## Base

- **Base name:** Book of Business Management
- **Base ID:** `app6O8peF5ywLe1GM`

## Accounts Table

- **Table:** Accounts
- **Table ID:** `tblr6UnvfaqfNvwyU`

Use this table to append the CRM activity note and update account progression fields.

| Field | Field ID | Use |
|---|---|---|
| Account Name | `fldOSLvopNOX6ae3Z` | Match the customer account |
| Activity notes | `flddz3lqUmEhLhmN5` | Append the CRM activity note |
| Engagement Status | `fldyrxDGOzWF3c7wm` | Current account relationship / engagement state |
| Outreach Step | `fldhX3nTqX4a2eKt8` | Current outreach/follow-up step |
| Meeting Sync established | `fld8kFpch7M4wGfpQ` | Indicates that a recurring customer sync/cadence has been confirmed. Use `yes` only when confirmed. |
| Cadence Frequency | `flddcR78KacwfLyk5` | Confirmed frequency of an established customer cadence. |
| Stage | `fldTYypHjPaFcatCi` | Legacy fallback only; do not update unless explicitly asked |

## Activity/Notes Table

- **Table:** Activity/Notes
- **Table ID:** `tblI5cCnIY63S6pZq`

Use this table to create one structured Meeting Notes record.

| Field | Field ID | Value |
|---|---|---|
| Account | `fldkJTerhbTfcObzR` | Select/link the matched account record |
| Activity Type | `fldfi0aH7CncdNtb9` | `Meeting Notes` |
| Date | `fld5Dd0gO8vHadVjl` | Date the meeting happened |
| Notes | `fldcfsEsMgFHoB8VH` | Structured meeting notes using the approved sections listed above |
| Title | `fldbf738tn5U18z7D` | Optional if required; use `Meeting Notes - [Account Name] - [Date]` |
| Next Steps | `flduD4nsj6ZkEXmLZ` | Optional; only populate when next steps are clearly stated and useful |

Important:
- The user specifically wants the important meeting sections stored in the Activity/Notes table.
- Do not create separate task rows by default.
- Engagement Status and Outreach Step are the primary account progression fields.
- Meeting Sync established and Cadence Frequency are cadence-detail fields. Update them only when a recurring cadence/sync is clearly confirmed or needs hygiene review.
- Do not update the legacy Stage field unless explicitly asked.

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

# Engagement Status / Outreach Step Update Logic

After creating the CRM activity note and Meeting Notes record, infer whether the meeting should update **Engagement Status** and/or **Outreach Step**.

Only update these fields when the meeting clearly supports the change.

## Meeting-based status mapping

| Meeting signal | Engagement Status | Outreach Step |
|---|---|---|
| Meeting is scheduled but has not happened yet | `Meeting scheduled` | `Meeting scheduled` |
| Meeting happened, customer engaged, no recurring cadence confirmed | `Connected - no cadence` | `Customer replied` |
| Meeting happened and regular sync / working cadence was agreed | `Cadence established` | `Cadence active` |
| Meeting confirmed next scheduled call but not a recurring cadence | `Meeting scheduled` | `Meeting scheduled` |
| Customer replied during scheduling process but meeting not booked | `Customer replied - scheduling` | `Customer replied` |
| Meeting exposed unresolved support issue or product blocker | `Support / blocker active` | Keep current Outreach Step unless outreach also changed |
| Meeting showed additional stakeholders or executive contact are needed | `Multithread required` | `Multithread` |
| Meeting confirmed churn, offboarding, non-renewal, or shutdown | `Churn / offboarding` | `Parked` |
| Meeting concluded no active outreach is needed for now | `Parked / no active outreach` | `Parked` |


## Meeting-based cadence update logic

When a meeting results in a recurring sync or cadence being agreed, update cadence fields in addition to Engagement Status and Outreach Step.

Use this mapping:

| Meeting outcome | Engagement Status | Outreach Step | Meeting Sync established | Cadence Frequency |
|---|---|---|---|---|
| Weekly recurring sync agreed | `Cadence established` | `Cadence active` | `yes` | `Weekly` |
| Bi-weekly / every two weeks / fortnightly recurring sync agreed | `Cadence established` | `Cadence active` | `yes` | `Bi-weekly` |
| Monthly recurring sync agreed | `Cadence established` | `Cadence active` | `yes` | `Monthly` |
| Quarterly business review / QBR cadence agreed | `Cadence established` | `Cadence active` | `yes` | `Quarterly` |
| Ad hoc / as-needed check-in cadence agreed | `Cadence established` | `Cadence active` | `yes` | `Ad hoc / as needed` |
| Recurring cadence agreed but frequency not stated | `Cadence established` | `Cadence active` | `yes` | `TBD / not confirmed` |
| Cadence paused or no recurring sync needed for now | `Parked / no active outreach` if no other active work remains | `Parked` | keep current value unless explicitly cancelled | `Paused` |

Rules:

- Do not set Meeting Sync established to `yes` for a one-off meeting.
- Do not set Cadence Frequency for a single meeting unless a recurring cadence was agreed.
- If recurring cadence is agreed but frequency is missing, set Cadence Frequency to `TBD / not confirmed` and include this in the Airtable update confirmation.
- If the meeting happened and customer is engaged but no cadence is agreed, use `Connected - no cadence` and keep cadence fields blank/unchanged.

## Status precedence

When multiple meeting signals appear, use this precedence order:

1. `Churn / offboarding`
2. `Support / blocker active`
3. `Cadence established`
4. `Meeting scheduled`
5. `Customer replied - scheduling`
6. `Multithread required`
7. `Connected - no cadence`
8. `Parked / no active outreach`

Examples:
- If a meeting happened and revealed a technical blocker, use `Support / blocker active`, not `Connected - no cadence`.
- If a meeting happened and established weekly syncs, use `Cadence established` and `Cadence active`.
- If a meeting happened and the next step is to find a higher stakeholder, use `Multithread required` and `Multithread` unless there is a higher-priority blocker.

## Guardrails

- Do not auto-update Outreach Step to `FU 1`, `FU 2`, `FU 3`, or `FU 4` from a meeting unless the transcript clearly says that follow-up was sent.
- Do not overwrite `Support / blocker active` unless the meeting clearly says the blocker was resolved.
- Do not overwrite `Churn / offboarding` unless the meeting clearly says the account has re-engaged or the churn/offboarding status is no longer valid.
- Do not update the legacy Stage field.
- If no clear status change is supported, leave Engagement Status and Outreach Step unchanged.
- If no recurring cadence is clearly confirmed, leave Meeting Sync established and Cadence Frequency unchanged.

---

# Workflow

## Step 1 — Identify Account and Meeting Date

Extract from the user input:

- Account name
- Meeting date
- Customer participants, if obvious
- Internal participants, if obvious
- Main discussion topics
- Customer asks
- Agreed next steps
- Meeting outcome signals that may affect Engagement Status / Outreach Step

If the account name is missing, ask one short clarification question:

```text
Which account should I update this under?
```

If the meeting date is missing, use today's date in Asia/Kolkata and keep moving. Do not ask a clarification question only for the date unless the user specifically asks for exact historical logging.

---

## Step 2 — Create the CRM Activity Note for Accounts → Activity Notes

Create a concise CRM-style account update for the **Accounts** table → **Activity notes** field.

Format:

```text
DD Month YYYY - [2–4 sentence CRM activity note]
```

Rules:

- Write in a factual CSM style.
- Capture what was discussed, what changed, and what the next step is.
- Include product names, blockers, dates, numbers, owners, and technical terms exactly when provided.
- Mention customer sentiment or risk only when it was clearly discussed or strongly evident.
- Do not include internal-only strategy, speculation, or negative commentary.
- Keep it short enough to work inside a running account activity log.

Example:

```text
29 April 2026 - Met with the customer to review the workflow setup and current Salesforce mapping requirements. The customer confirmed they want to prioritize high-quality account/contact creation and avoid creating accounts without usable contacts. Next step is to validate the workflow logic, confirm prioritization options, and follow up with a recommended setup path.
```

---

## Step 3 — Append CRM Note and Update Status Fields on Accounts Table

1. Search the Accounts table for the account using `Account Name` (`fldOSLvopNOX6ae3Z`).
2. Confirm the returned account strongly matches the requested account.
3. Pull current values for:
   - Activity notes (`flddz3lqUmEhLhmN5`)
   - Engagement Status (`fldyrxDGOzWF3c7wm`)
   - Outreach Step (`fldhX3nTqX4a2eKt8`)
   - Meeting Sync established (`fld8kFpch7M4wGfpQ`)
   - Cadence Frequency (`flddcR78KacwfLyk5`)
   - Stage (`fldTYypHjPaFcatCi`) as legacy fallback only
4. Append the new CRM activity note to the bottom of existing Activity notes.
5. Use two line breaks between the existing content and the new note.
6. Infer new Engagement Status, Outreach Step, Meeting Sync established, and Cadence Frequency if the meeting clearly supports a change.
7. Update the Airtable record.

Do not overwrite existing Activity notes unless the user explicitly asks to replace them.

Do not update the Accounts table Stage field.

Do not create separate task records.

---

## Step 4 — Create the Activity/Notes Meeting Notes Record

Create one new record in the Activity/Notes table.

Required values:

```text
Account: [matched account record]
Activity Type: Meeting Notes
Date: [meeting date]
Notes: [structured meeting notes using the approved sections]
```

The **Notes** field should use this structure:

```text
Meeting Overview
- Account: [Account Name]
- Meeting date: [Meeting Date]
- Meeting purpose: [short purpose]
- Participants: [participants if known]

Executive Summary
[2–4 sentence summary of the meeting outcome, customer sentiment, and why it matters.]

Customer Stated Priorities
- [priority / goal / success criteria]
- [priority / goal / success criteria]

Key Discussion / Decisions
- [important topic discussed]
- [decision or alignment reached]
- [workflow, product, reporting, renewal, or technical item reviewed]

Risks / Blockers / Signals
- [risk, blocker, concern, or signal]

What I Should Do Next as the CSM
- [CSM-owned next step]
- [CSM-owned next step]

Open Questions / Needs Confirmation
- [open item or assumption to confirm]
```

Formatting rules for the Notes field:

- Keep the sections readable and practical.
- Use only sections that have real content.
- If there are no risks, blockers, or signals, omit that section.
- If there are no open questions, omit that section.
- Keep “Key Discussion / Decisions” concise; do not recreate a long transcript summary.
- Include important customer asks, technical terms, dates, numbers, owners, product names, and deadlines exactly when provided.
- Do not include filler, greetings, transcript noise, or internal-only commentary.

Optional fields:

- Title: Use only if Airtable requires or benefits from it.
- Next Steps: Use only if next steps are clearly stated and useful as a separate field.

---

# Customer Follow-Up Email

Always draft a customer-facing follow-up email from the meeting context unless the user says not to.

The email must be output as an editable writing block. Do not put the customer follow-up email in a plain markdown code block. Use `variant="email"`, put the subject in writing-block metadata, and use a unique random 5-digit writing-block `id`.

The email should feel warm, human, and professional. It should make the customer feel heard and clearly reinforce the next steps without sounding robotic.

## Required Email Elements

- Subject line
- Warm opening that references the meeting naturally
- Short relationship-building sentence where it fits
- Concise recap of what was discussed
- Important next steps only
- Clear close that reinforces partnership

## Email Style Rules

- Do not over-format the email.
- Do not default to too many headings.
- Use bullets only when helpful.
- Keep it concise enough for a customer to read.
- Do not include internal risks, hidden strategy, or sensitive internal commentary.
- Do not include negative commentary about the customer.
- Do not include every transcript detail.

## Email Structure Options

Choose the structure based on meeting type.

### Intro / Relationship-Building Meeting

Use a warm, simple recap focused on connection, priorities, and next steps.

### Strategic Planning / Account Direction Meeting

Use a recap focused on priorities, alignment, business outcomes, and follow-through.

### Tactical Working Session / Product Enablement Meeting

Use a recap focused on what was reviewed, what was decided, and what needs to happen next.

### Support / Blocker Meeting

Acknowledge the issue clearly, summarize what was captured, and state what 6sense will do next.

---

# Final Chat Output Format

Use this exact order.

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
Meeting Overview
- Account: [Account Name]
- Meeting date: [Meeting Date]
- Meeting purpose: [short purpose]
- Participants: [participants if known]

Executive Summary
[2–4 sentence summary]

Customer Stated Priorities
- [priority]
- [priority]

Key Discussion / Decisions
- [topic / decision]
- [topic / decision]

Risks / Blockers / Signals
- [only if applicable]

What I Should Do Next as the CSM
- [CSM-owned next step]
- [CSM-owned next step]

Open Questions / Needs Confirmation
- [only if applicable]
```

### Customer Follow-Up Email

:::writing{variant="email" id="[unique 5-digit id]" subject="[Subject]"}
Hi [Customer Name],

[email body]

Best,
Ranjodh
:::

[One-line Airtable update confirmation or failure note, including Engagement Status / Outreach Step / Meeting Sync established / Cadence Frequency changes if applied.]
````

Important: Do not add extra sections before or after these unless the user asks.

---

# Airtable Update Confirmation Format

If only notes were updated:

```text
Updated [Account Name] in Airtable with the CRM activity note and Meeting Notes record. No Engagement Status, Outreach Step, Meeting Sync established, or Cadence Frequency change was applied.
```

If status fields were updated:

```text
Updated [Account Name] in Airtable with the CRM activity note, Meeting Notes record, and status/cadence fields.
Engagement Status: [old value → new value, or No change]
Outreach Step: [old value → new value, or No change]
Meeting Sync established: [old value → new value, or No change]
Cadence Frequency: [old value → new value, or No change]
```

If only one status or cadence field changed, still show the current cadence state when cadence is relevant to the meeting outcome.

---

# If the User Asks for Only One Output

Respect narrower requests:

- “Only CRM note” → output only the CRM Activity Note. If Airtable update is implied, still update status fields where applicable and add one short confirmation.
- “Only Airtable note” → output only the Airtable Meeting Notes Record.
- “Only email” → output only the Customer Follow-Up Email as an editable writing block.
- “Update Airtable only” → update Airtable and provide one short confirmation, including status changes if applied.
- “No email” → skip the follow-up email.
- “Do not update statuses” / “notes only” → do not update Engagement Status or Outreach Step.

---

# Quality Checklist

Before finalizing, verify:

- The account name is identified or the user has been asked for it.
- The CRM note is short, factual, and suitable for the Accounts table Activity notes field.
- The Activity/Notes record uses Activity Type = `Meeting Notes`.
- The Activity/Notes record uses the meeting date, not an unrelated date.
- The Notes field contains only the approved important sections from the old meeting summary workflow.
- The Notes field does not contain the CRM note, Slack update, customer email, very short version, or transcript noise.
- The follow-up email is customer-safe, warm, concise, and rendered as an editable writing block.
- Engagement Status and Outreach Step were updated only when clearly supported.
- Meeting Sync established and Cadence Frequency were updated only when a recurring cadence/sync was clearly confirmed.
- The legacy Stage field was not updated unless the user explicitly asked.
- No old 11-section summary was produced in the chat.
- No Slack update, task records, or legacy Stage updates were created unless explicitly requested.

---

# Edge Cases

## Account Not Found in Airtable

Do not guess. Provide the three output sections and say:

```text
I could not find a matching Airtable account for [Account Name], so I did not update Airtable.
```

## Multiple Account Matches

Ask one short clarification question and do not update Airtable until the user confirms the correct account.

## No Clear Meeting Date

Use today's date in Asia/Kolkata and proceed.

## No Clear Customer Follow-Up Needed

Still draft a simple follow-up email unless the user says not to. Keep it light and focused on the main takeaway and next step.

## Support Issue Discussed

Do not create a support ticket unless the user explicitly asks. Mention in the CRM note and follow-up email that 6sense will follow up or investigate, if appropriate.

If the support issue is active or unresolved, update Engagement Status to `Support / blocker active`.

## Tasks Detected

Do not create task records by default. Include next steps inside the CRM note, Activity/Notes Notes field, and customer follow-up email only.

## Meeting Did Not Happen

If the input says the meeting was scheduled but not completed, use:

```text
Engagement Status = Meeting scheduled
Outreach Step = Meeting scheduled
```

Do not create completed-meeting notes unless there was actual meeting content.

---

# Final Rule

The skill should give Ranjodh a clean CRM activity update, a structured Airtable Meeting Notes record with the important meeting sections, a customer-safe follow-up email, accurate Engagement Status / Outreach Step updates when the meeting clearly changes account progression, and accurate Meeting Sync established / Cadence Frequency updates when the meeting clearly confirms a recurring cadence.

Do not recreate the long meeting-summary workflow in the chat. Do not update legacy Stage. Keep the account progression fields current without guessing.
