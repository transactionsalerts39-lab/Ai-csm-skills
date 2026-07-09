---
name: customer-meeting-prep-brief
description: >
  Pull Airtable account notes, prior meeting notes, Customer Tasks, recent activity, open asks,
  risks, and next steps into a short pre-meeting brief for Ranjodh. Use this before customer
  meetings when the goal is to quickly understand what matters without reading long notes.
  Also supports optional deep prep mode when the user asks for deeper context, full prep,
  strategy, dossier-style prep, or all relevant account history.
---

# Customer Meeting Prep Brief — Customer Tasks V3

## Purpose

Create a simple, fast pre-meeting brief from Airtable so Ranjodh can prepare for a customer meeting in under 3 minutes.

The output should answer:

- What is going on with this account?
- What are the active Customer Tasks?
- What might the customer be waiting on from Ranjodh or 6sense?
- What was discussed in prior meetings?
- What should Ranjodh remember, ask, and drive?
- Are there risks, blockers, renewal concerns, or open asks?

Customer Tasks is the primary source for open work. Activity notes and Detailed Notes are supporting context.

---

## Triggers

Use this skill when the user says things like:

- `/meeting prep`
- `/prep call`
- `/customer prep`
- `/pre-call brief`
- `/prep me for [account]`
- `/meeting brief`
- `/before my meeting with [account]`
- `/quick account prep`
- `/meeting prep [account] deep`
- `/deep meeting prep [account]`
- `/full prep [account]`
- `/account dossier [account]`
- `/meeting strategy [account]`

---

## Prep Depth Modes

### Default Quick Prep Mode

Use this by default. It should help Ranjodh prepare in under 3 minutes. Pull the most relevant account context, compress aggressively, and focus on what to remember, ask, and drive in the meeting.

### Deep Prep Mode

Use Deep Prep Mode when the user says or implies any of the following:

- `deep`
- `full prep`
- `account dossier`
- `dossier`
- `meeting strategy`
- `strategy prep`
- `all context`
- `full history`
- `go deeper`
- `comprehensive prep`
- `exec prep`
- `renewal prep`

Deep Prep Mode should include:

- a longer account timeline
- recent and historically important Detailed Notes
- active and recently closed Customer Tasks
- recurring themes across prior meetings
- stakeholder and relationship map where available
- highest known contact and path to higher contact if captured
- open asks and ownership
- risk history and current risk posture
- renewal/commercial context when available
- support/product blockers and whether they are still open
- customer priorities and expected outcomes
- likely customer expectations for the meeting
- recommended meeting strategy and talk track
- smart questions to ask
- gaps or missing context that Ranjodh should confirm

Do not invent missing stakeholders, risks, renewal details, task status, or customer sentiment. If the notes do not support something, say it is not captured.

---

## Airtable Source of Truth

Base:

- Book of Business Management
- Base ID: `app6O8peF5ywLe1GM`

Tables:

- Accounts: `tblr6UnvfaqfNvwyU`
- Customer Tasks: `tblWUzwjGM4pwhAqR`
- Detailed Notes: `tblI5cCnIY63S6pZq`

Always use `schema/airtable-schema-map.md` for current field IDs and allowed values.

### Accounts fields

- Account Name: `fldOSLvopNOX6ae3Z`
- Activity notes: `flddz3lqUmEhLhmN5`
- Engagement Status: `fldyrxDGOzWF3c7wm`
- Outreach Step: `fldhX3nTqX4a2eKt8`
- Meeting Sync established: `fld8kFpch7M4wGfpQ`
- Cadence Frequency: `flddcR78KacwfLyk5`
- Stage: `fldTYypHjPaFcatCi`
- ACV: `fldjieKzPumeF6afD`
- Renewal Date: `fldPmw5pHDNDgZYgA`
- Churn Risk: `fldy4GIC8xDuPjS8y`
- Last Activity Date: `fld2jD1HJm9RRwNBW`
- Task status: `fldaYegYsT0eA3NAK`
- Current Active CSM: `fldTQWeUcqj5HQoAH`

### Customer Tasks fields

- Task Title: `fldbPKh3KNG9vbeXS`
- Account: `fldMIaXj2PtqHkmNk`
- Owner: `fldVF6TQNNaPmv8qf`
- Status: `fldFbZYJvZUZmrGf2`
- Priority: `fldGixN6MmFNAbTdG`
- Due Date: `fldm4uWSm23HVxZPC`
- Customer Waiting?: `fldDXgxychawzNCZn`
- Source Type: `fldRaStx1IoK4ooqv`
- Source Date: `fldp9gpKEdSIywADe`
- Source Summary: `fldKEtfu2JfA5VTKK`
- Source Detail Note: `fldo4zvmVhOXqed2p`
- Completion Evidence: `fldsWVTfAAagVBTPT`
- Completed Date: `fldcOYlYd9dfmyl5K`
- Needs Review: `fld5SXTsSrQNl96t2`
- Last Updated From: `fldg0rM7UT6Jt148a`

### Detailed Notes fields

- Account: `fldkJTerhbTfcObzR`
- Title: `fldbf738tn5U18z7D`
- Notes: `fldcfsEsMgFHoB8VH`
- Activity Type: `fldfi0aH7CncdNtb9`
- Date: `fld5Dd0gO8vHadVjl`
- Next Steps: `flduD4nsj6ZkEXmLZ`
- Customer Tasks: `fld5rGBShRUYrYIiR`

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

### Step 2 — Pull Airtable Context

Pull from Accounts first:

- Account Name
- Activity notes
- Engagement Status
- Outreach Step
- Meeting Sync established
- Cadence Frequency
- Stage
- ACV
- Renewal Date
- Churn Risk
- Last Activity Date
- Task status
- Current Active CSM

Then pull Customer Tasks linked to the account.

For Quick Prep Mode, prioritize active Customer Tasks:

- `Open`
- `In Progress`
- `Waiting on Internal Team`
- `Waiting on Customer`
- `Blocked`
- `Needs Review`

Also include recently completed tasks only if they explain what the customer may expect next.

For Deep Prep Mode, pull:

- all active Customer Tasks
- recently completed Customer Tasks from the last 30–60 days when relevant
- any tasks with Customer Waiting? checked
- any P1/P2 tasks
- any support/blocker/renewal tasks

Then pull from Detailed Notes.

For Quick Prep Mode, pull:

- The 3–5 most recent meeting notes or activity records
- Any records with Activity Type = Meeting Notes
- Any records with clear Next Steps
- Any records that mention blockers, renewal, risk, support, follow-up, or customer asks

For Deep Prep Mode, pull broader context:

- The 10–15 most recent relevant Detailed Notes records when available
- Older Detailed Notes records that mention renewals, churn risk, executive stakeholders, procurement, pricing, support blockers, product gaps, escalations, implementation issues, QBR/EBR, or strategic priorities
- All clearly open Next Steps or unresolved customer asks
- Any notes that explain how the relationship, risk, task status, or account direction changed over time

### Step 3 — Extract Only What Matters

From Airtable, identify:

- Last meaningful customer interaction
- Active Customer Tasks
- Customer-waiting tasks
- Tasks that may need to be checked off before the meeting
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

Do not create a long history in Quick Prep Mode. Compress aggressively.

In Deep Prep Mode, create a richer synthesis, but still avoid dumping every historical note. Prioritize patterns, decisions, risks, stakeholders, open loops, Customer Tasks, and what Ranjodh should do in the meeting.

---

## Required Output Format — Quick Prep Mode

Always use this format by default:

```md
# Meeting Prep Brief — [Account Name]

## Read This First
[3–5 bullets only. Include the most important Customer Task/customer-waiting item if one exists.]

## Account Snapshot
| Field | Detail |
|---|---|
| Engagement Status | [Engagement Status] |
| Outreach Step | [Outreach Step] |
| Cadence | [Meeting Sync established + Cadence Frequency] |
| Stage | [Stage] |
| ACV | [ACV] |
| Renewal Date | [Renewal Date] |
| Churn Risk | [Churn Risk] |
| Task Status | [Task status + active Customer Task count] |
| Last Meaningful Activity | [date + short context] |

## Active Customer Tasks
| Priority | Task | Owner | Status | Due / Timing | Customer Waiting? |
|---|---|---|---|---|---|
| [P1/P2/P3] | [Task Title] | [Owner] | [Status] | [Due Date / No due date] | [Yes/No] |

## Recent Context
| Date | What happened | Why it matters |
|---|---|---|
| [Date] | [short summary] | [impact / relevance] |

## Open Threads
- [Open ask, blocker, task, or follow-up]
- [Open ask, blocker, task, or follow-up]

## Risks / Watchouts
- [Only include real risks or concerns from Airtable]
- [Omit this section if no real risk exists]

## Suggested Meeting Focus
- [Agenda item 1]
- [Agenda item 2]
- [Agenda item 3]

## Questions to Ask
- [Question 1]
- [Question 2]
- [Question 3]

## What to Drive Before Ending the Call
- [Specific outcome / next step]
```

If no active Customer Tasks exist, write:

```text
No active Customer Tasks captured for this account.
```

If a task appears completed but still active, include it under Active Customer Tasks and flag it as:

```text
Possible check-off needed — confirm via /update notes or /task centre.
```

---

## Deep Prep Output Additions

When using Deep Prep Mode, keep the Quick Prep sections and add:

```md
## Account Timeline
| Date | Event | Impact |
|---|---|---|

## Task History / Open Loops
| Task | Owner | Status | Evidence / Notes |
|---|---|---|---|

## Stakeholder / Relationship Map
| Person / Role | Signal | What to do with it |
|---|---|---|

## Strategy / Talk Track
[Practical point of view for the meeting]
```

Do not turn Deep Prep into a raw Airtable dump.

---

## Interpretation Rules

### Customer Tasks

- Active tasks are stronger evidence than old notes.
- Customer Waiting? checked means this should be surfaced high in the brief.
- P1/P2 tasks should appear before P3 tasks.
- A task owned by Customer should still appear if it affects what Ranjodh should ask or confirm.
- A task owned by Support/Internal Team should appear if Ranjodh needs to provide an update or manage customer expectations.
- Do not mark tasks complete in Meeting Prep. If completion evidence appears, recommend checking it off through `/update notes` or `/task centre`.

### Activity Notes and Detailed Notes

- Use Activity notes for the account narrative and fallback tasks not yet captured in Customer Tasks.
- Use Detailed Notes for meeting history and supporting context.
- Do not treat Last Activity Date alone as proof of customer activity because it is a last-modified timestamp.

### Renewal / risk

- If the account is near renewal, emphasize task/risk/commercial next steps.
- If Churn Risk conflicts with notes, say it needs confirmation; do not silently resolve the conflict.

---

## Edge Cases

### No active tasks

Still provide the prep brief. Do not invent tasks.

### Customer waiting task exists

Put it in Read This First and Active Customer Tasks.

### Task seems done but still open

Do not close it. Flag it for review:

```text
Possible check-off needed — confirm via /update notes or /task centre.
```

### Account has no recent notes

Say that recent context is limited and focus on current tasks, renewal/risk metadata, and what to confirm.

### Multiple account matches

Ask one short clarification question and do not continue until the user confirms.

---

## Final Rule

This skill is for fast meeting readiness. Customer Tasks tells Ranjodh what may be owed; Activity notes and Detailed Notes explain why. Keep the brief practical, short by default, and focused on what to remember, ask, and drive.
