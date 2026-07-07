---
name: customer-meeting-prep-brief
description: >
  Pull Airtable account notes, prior meeting notes, recent activity, open asks, risks,
  and next steps into a short pre-meeting brief for Ranjodh. Use this before customer
  meetings when the goal is to quickly understand what matters without reading long notes.
---

# Customer Meeting Prep Brief

## Purpose

Create a simple, fast pre-meeting brief from Airtable so Ranjodh can prepare for a customer meeting in under 3 minutes.

Use this skill when the user says things like:

- `/meeting prep`
- `/prep call`
- `/customer prep`
- `/pre-call brief`
- `/prep me for [account]`
- `/meeting brief`
- `/before my meeting with [account]`
- `/quick account prep`

---

## Core Goal

The output should answer:

- What is going on with this account?
- What was discussed in prior meetings?
- What is the customer likely expecting from me?
- What should I ask or drive in the meeting?
- Are there any risks, blockers, renewal concerns, or open asks?

Keep the output short. This is not a full account dossier.

---

## Airtable Source of Truth

Use Airtable as the source.

Base:
- Book of Business Management
- Base ID: `app6O8peF5ywLe1GM`

Primary table:
- Accounts
- Table ID: `tblr6UnvfaqfNvwyU`

Use these fields:
- Account Name: `fldOSLvopNOX6ae3Z`
- Activity notes: `flddz3lqUmEhLhmN5`
- Stage: `fldTYypHjPaFcatCi`
- ACV: `fldjieKzPumeF6afD`
- Renewal Date: `fldPmw5pHDNDgZYgA`
- Churn Risk: `fldy4GIC8xDuPjS8y`
- Last Activity Date: `fld2jD1HJm9RRwNBW`
- Task status: `fldaYegYsT0eA3NAK`

Secondary table:
- Detailed Notes
- Table ID: `tblI5cCnIY63S6pZq`

Use these fields:
- Account: `fldkJTerhbTfcObzR`
- Title: `fldbf738tn5U18z7D`
- Notes: `fldcfsEsMgFHoB8VH`
- Activity Type: `fldfi0aH7CncdNtb9`
- Date: `fld5Dd0gO8vHadVjl`
- Next Steps: `flduD4nsj6ZkEXmLZ`

---

## Workflow

### Step 1 — Identify the Account

Extract the account name from the user’s request.

If the user includes an account name anywhere in the request, use that account name and do not ask for confirmation unless multiple Airtable matches are found.

If the account name is missing, ask:

```text
Which customer account should I prep for?
```

If multiple Airtable accounts match, ask the user to choose the correct one.

---

### Step 2 — Pull Airtable Context

Pull from the Accounts table first:
- Account Name
- Activity notes
- Stage
- ACV
- Renewal Date
- Churn Risk
- Last Activity Date
- Task status

Then pull from Detailed Notes:
- The 3–5 most recent meeting notes or activity records
- Any records with Activity Type = Meeting Notes
- Any records with clear Next Steps
- Any records that mention blockers, renewal, risk, support, follow-up, or customer asks

The Accounts table Activity notes field is the primary source. Detailed Notes is supporting context.

---

### Step 3 — Extract Only What Matters

From Airtable, identify:

- Last meaningful customer interaction
- Prior meeting themes
- Customer priorities
- Open asks
- CSM-owned follow-ups
- Customer-owned follow-ups
- Product, support, reporting, campaign, integration, or workflow blockers
- Renewal or churn risk signals
- Stakeholders mentioned
- Promised next steps
- Anything the customer may expect Ranjodh to remember

Do not create a long history. Compress aggressively.

---

## Required Output Format

Always use this format by default:

```md
# Meeting Prep Brief — [Account Name]

## Read This First
[3–5 bullets only. The most important context before the meeting.]

## Account Snapshot
| Field | Detail |
|---|---|
| Stage | [Stage] |
| ACV | [ACV] |
| Renewal Date | [Renewal Date] |
| Churn Risk | [Churn Risk] |
| Last Meaningful Activity | [date + short context] |

## Recent Context
| Date | What happened | Why it matters |
|---|---|---|
| [Date] | [short summary] | [impact / relevance] |

## Open Threads
- [Open ask, blocker, or follow-up]
- [Open ask, blocker, or follow-up]

## Risks / Watchouts
- [Only include real risks or concerns from Airtable]
- [Omit this section if no real risk exists]

## Suggested Meeting Focus
- [Agenda item 1]
- [Agenda item 2]
- [Agenda item 3]

## Questions to Ask
- [Smart customer-facing question]
- [Smart customer-facing question]
- [Smart customer-facing question]

## My Recommended Move
[1 short paragraph telling Ranjodh what to drive in the meeting.]
```

---

## Length Rules

Keep this brief enough to read quickly.

Default limits:
- Read This First: max 5 bullets
- Account Snapshot: max 5 rows
- Recent Context: max 5 rows
- Open Threads: max 5 bullets
- Risks / Watchouts: max 4 bullets
- Suggested Meeting Focus: max 4 bullets
- Questions to Ask: max 5 bullets
- My Recommended Move: max 4 sentences

If there is a lot of history, summarize instead of listing everything.

---

## Interpretation Rules

### Last Activity Date

The Airtable Last Activity Date is a record update signal, not always a true customer touch.

Use the Activity notes and Detailed Notes dates to determine the last meaningful customer activity when possible.

### Meeting Notes

Prioritize records where:
- Activity Type = Meeting Notes
- Notes include customer priorities, next steps, blockers, or decisions
- Date is recent
- The content changes what Ranjodh should do in the meeting

### Open Threads

Treat these as open threads:
- “I’ll send”
- “Need to follow up”
- “Ranjodh to”
- “Customer asked”
- “Support ticket”
- “Waiting on update”
- “Need to confirm”
- “Schedule”
- “Share resource”
- “Check internally”
- “Loop in”

Do not include items that later notes clearly show were completed.

### Risks / Watchouts

Include only real signals such as:
- Churn Risk = Yellow or Confirmed churn
- Renewal date is close
- Customer frustration
- No response / FU 2 / FU 3
- Unresolved support blocker
- Product gap
- Stakeholder transition
- Low engagement
- Commercial concern
- Misaligned expectations

Do not invent risk.

---

## Output Style

The tone should be:
- Direct
- Practical
- Easy to scan
- Internal-facing
- CSM-friendly

Do not write a polished essay. Do not create a full account dossier. Do not include every historical note.

The user should be able to read the output immediately before a meeting and know what to say.

---

## Edge Cases

### No Airtable notes found

Say:

```text
I found the account, but there are no useful Airtable notes to prep from.
```

Then provide whatever account metadata is available.

### Account not found

Say:

```text
I could not find a matching Airtable account for [Account Name].
```

Ask the user for the exact Airtable account name.

### No clear meeting purpose

Infer from recent activity and say:

```text
Meeting purpose is inferred from recent Airtable notes.
```

### User asks for “super short”

Use only:

```md
# Quick Meeting Prep — [Account Name]

## Read This First
- [bullet]
- [bullet]
- [bullet]

## Open Threads
- [bullet]
- [bullet]

## Recommended Move
[1–2 sentences]
```

---

## Final Rule

This skill is for meeting readiness, not documentation.

Prioritize the few things Ranjodh needs to remember, ask, and drive in the customer meeting.
