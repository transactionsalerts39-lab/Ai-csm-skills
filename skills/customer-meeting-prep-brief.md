---
name: customer-meeting-prep-brief
description: >
  Pull Airtable account notes, prior meeting notes, recent activity, open asks, risks,
  and next steps into a short pre-meeting brief for Ranjodh. Use this before customer
  meetings when the goal is to quickly understand what matters without reading long notes.
  Also supports optional deep prep mode when the user asks for deeper context, full prep,
  strategy, dossier-style prep, or all relevant account history.
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
- `/meeting prep [account] deep`
- `/deep meeting prep [account]`
- `/full prep [account]`
- `/account dossier [account]`
- `/meeting strategy [account]`

---

## Core Goal

The output should answer:

- What is going on with this account?
- What was discussed in prior meetings?
- What is the customer likely expecting from me?
- What should I ask or drive in the meeting?
- Are there any risks, blockers, renewal concerns, or open asks?

Keep the output short by default. This is not a full account dossier unless the user explicitly asks for deep prep, full prep, dossier-style prep, strategy, or all relevant context.

---

## Prep Depth Modes

### Default Quick Prep Mode

Use this mode by default. It should help Ranjodh prepare in under 3 minutes. Pull the most relevant account context, compress aggressively, and focus on what to remember, ask, and drive in the meeting.

Default quick prep should not become a full account history or dossier.

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

Deep Prep Mode should still be practical, but it should pull and synthesize more context than the default brief. Use it when Ranjodh needs a stronger point of view before a high-stakes customer meeting, renewal conversation, executive discussion, escalation, or strategic planning call.

Deep Prep Mode should include:

- a longer account timeline
- recent and historically important Detailed Notes
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

Do not invent missing stakeholders, risks, renewal details, or customer sentiment. If the notes do not support something, say it is not captured.

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

Then pull from Detailed Notes.

For Default Quick Prep Mode, pull:
- The 3–5 most recent meeting notes or activity records
- Any records with Activity Type = Meeting Notes
- Any records with clear Next Steps
- Any records that mention blockers, renewal, risk, support, follow-up, or customer asks

For Deep Prep Mode, pull broader context:
- The 10–15 most recent relevant Detailed Notes records when available
- Older Detailed Notes records that mention renewals, churn risk, executive stakeholders, procurement, pricing, support blockers, product gaps, escalations, implementation issues, QBR/EBR, or strategic priorities
- All clearly open Next Steps or unresolved customer asks
- Any notes that explain how the relationship, risk, or account direction changed over time

The Accounts table Activity notes field is the primary source. Detailed Notes is supporting context. In Deep Prep Mode, use both sources to build a practical account narrative, not a raw chronological dump.

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

Do not create a long history in Default Quick Prep Mode. Compress aggressively.

In Deep Prep Mode, create a richer synthesis, but still avoid dumping every historical note. Prioritize patterns, decisions, risks, stakeholders, open loops, and what Ranjodh should do in the meeting.

---

## Required Output Format

Always use this format by default for Quick Prep Mode:

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

## Deep Prep Output Format

Use this format when Deep Prep Mode is triggered:

```md
# Deep Meeting Prep — [Account Name]

## Read This First
[5–7 bullets with the most important account context, risks, and recommended stance.]

## Account Snapshot
| Field | Detail |
|---|---|
| Stage | [Stage] |
| ACV | [ACV] |
| Renewal Date | [Renewal Date] |
| Churn Risk | [Churn Risk] |
| Last Meaningful Activity | [date + short context] |
| Current Relationship / Cadence | [what is captured or not captured] |

## Account Timeline
| Date | What happened | Why it matters now |
|---|---|---|
| [Date] | [short summary] | [current relevance] |

## Stakeholders / Relationship Map
| Person / Role | What we know | Why it matters |
|---|---|---|
| [Name or role] | [context from notes] | [influence / action needed] |

## Open Threads & Ownership
| Thread | Owner | Status / Next Move |
|---|---|---|
| [open item] | [Ranjodh / customer / internal] | [next action] |

## Risks / Watchouts
- [risk with evidence from Airtable]
- [risk with evidence from Airtable]

## Customer Priorities / Expected Outcomes
- [priority or outcome]
- [priority or outcome]

## Likely Customer Expectations for This Meeting
- [what they likely expect based on notes]
- [what they may ask about]

## Suggested Meeting Strategy
- [how Ranjodh should frame the meeting]
- [what to drive]
- [what to avoid or be careful about]

## Questions to Ask
- [smart question]
- [smart question]
- [smart question]

## Recommended Talk Track
[Short internal-facing talk track Ranjodh can use to open or steer the meeting.]

## My Recommended Move
[Clear recommendation on what Ranjodh should try to achieve in the meeting.]

## Gaps / Needs Confirmation
- [missing context or uncertain assumption]
```

---

## Length Rules

Keep this brief enough to read quickly.

Default Quick Prep limits:
- Read This First: max 5 bullets
- Account Snapshot: max 5 rows
- Recent Context: max 5 rows
- Open Threads: max 5 bullets
- Risks / Watchouts: max 4 bullets
- Suggested Meeting Focus: max 4 bullets
- Questions to Ask: max 5 bullets
- My Recommended Move: max 4 sentences

Deep Prep limits:
- Read This First: max 7 bullets
- Account Timeline: max 10 rows unless the user asks for all history
- Stakeholders / Relationship Map: max 8 rows
- Open Threads & Ownership: max 8 rows
- Risks / Watchouts: max 6 bullets
- Customer Priorities / Expected Outcomes: max 6 bullets
- Suggested Meeting Strategy: max 5 bullets
- Questions to Ask: max 8 bullets
- Recommended Talk Track: max 2 short paragraphs
- My Recommended Move: max 5 sentences

If there is a lot of history, summarize instead of listing everything. Deep Prep should be more complete than Quick Prep, but it should still be a synthesis, not a raw notes export.

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

Do not write a polished essay. Do not create a full account dossier in Default Quick Prep Mode. Do not include every historical note.

In Deep Prep Mode, a dossier-style output is allowed, but it must remain practical and meeting-focused. The user should still be able to quickly understand what to say, what to ask, what to avoid, and what outcome to drive.

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
