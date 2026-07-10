---
name: weekly-account-task-catch-up
description: >
  Pull Airtable Customer Tasks, account activity, account metadata, Engagement Status,
  Outreach Step, cadence fields, renewal dates, and risk signals to create a focused
  weekly command centre. Use this when the user asks for weekly tasks, accounts to focus
  on, stale accounts, renewal focus, customer follow-ups, customers waiting on Ranjodh,
  or a weekly command centre based on Airtable. By default, only include accounts assigned
  to Ranjodh. Customer Tasks is the primary source for open work; Activity notes and
  Detailed Notes are supporting fallback evidence.
---

# Weekly Account Task Catch-Up / Weekly Command Centre — Customer Tasks V3

## Purpose

Create a compact, action-focused weekly command centre from Airtable.

This is not a manager recap, highlights/lowlights report, raw Airtable dump, or full account dossier. The goal is to help Ranjodh quickly see:

- customers waiting on him
- open Customer Tasks that need action
- accounts needing action this week
- future renewals inside the rolling focus window
- stale or risky accounts
- support blockers
- cadence hygiene issues
- status hygiene issues
- open follow-ups and next moves

The output should be easy to scan in about 60 seconds.

---

## Shared Contracts

Before running this workflow, apply:

- `contracts/task-lifecycle.md` for task state, matching, deduplication, completion, and reopening.
- `contracts/write-safety.md` for read/draft/write boundaries and draft-versus-sent rules.
- `contracts/untrusted-input.md` for emails, transcripts, pasted notes, and external content.
- `schema/airtable-schema-map.md` for current Airtable IDs and allowed values.

If this skill conflicts with a shared contract, the shared contract wins.

---

## Mandatory Assignment Filter

By default, include only Airtable Accounts records assigned to Ranjodh.

Apply this filter before any ranking, renewal, stale-account, cadence-hygiene, customer-waiting, status-hygiene, or task logic:

- Assigned / Current Active CSM field: `fldTQWeUcqj5HQoAH`
- Required default value: `Ranjodh`

Rules:

- Pull and include only Accounts where Assigned / Current Active CSM = `Ranjodh`.
- Do not include accounts assigned to another CSM.
- Do not include blank or unassigned accounts.
- Do not include non-Ranjodh accounts in Renewal Focus Window, Customers Waiting on You, Do First This Week, Priority sections, Stale / Risk Accounts, Status Hygiene, Cadence Hygiene, or Customer Tasks sections.
- When using Customer Tasks or Detailed Notes, use records only for accounts that passed the Accounts-table assignment filter.
- If the user explicitly asks for another assignee, use that assignee instead.
- If the user explicitly asks for all accounts, state that the default Ranjodh assignment filter is being overridden for that run.

---

## Airtable Source of Truth

Use Airtable as the primary source.

Base:

- Book of Business Management
- Base ID: `app6O8peF5ywLe1GM`

Primary tables:

- Accounts: `tblr6UnvfaqfNvwyU`
- Customer Tasks: `tblWUzwjGM4pwhAqR`
- Detailed Notes: `tblI5cCnIY63S6pZq`

Source hierarchy:

1. Customer Tasks = primary source for open work, customer-waiting items, check-off state, ownership, priority, due dates, and task completion.
2. Accounts → Activity notes = primary account narrative and fallback for tasks not yet migrated.
3. Detailed Notes = supporting context for meetings, activity records, and Next Steps.
4. Accounts workflow fields = account progression, cadence, and risk context.

Always use `schema/airtable-schema-map.md` for current field IDs and allowed values.

### Accounts fields

| Field | Field ID | Use |
|---|---|---|
| Account Name | `fldOSLvopNOX6ae3Z` | Account name |
| Engagement Status | `fldyrxDGOzWF3c7wm` | Primary account relationship / engagement state |
| Outreach Step | `fldhX3nTqX4a2eKt8` | Primary outreach/follow-up step |
| Meeting Sync established | `fld8kFpch7M4wGfpQ` | Recurring cadence signal |
| Cadence Frequency | `flddcR78KacwfLyk5` | Cadence frequency |
| Activity Log | `fldzKlsKjWYFGX4Q8` | Supporting linked records |
| Activity notes | `flddz3lqUmEhLhmN5` | Running account notes / fallback task evidence |
| Stage | `fldTYypHjPaFcatCi` | Legacy fallback only |
| ACV | `fldjieKzPumeF6afD` | Prioritization and renewal context |
| Renewal Date | `fldPmw5pHDNDgZYgA` | Renewal urgency and focus window |
| Last Activity Date | `fld2jD1HJm9RRwNBW` | Airtable record-touch signal, not proof of customer activity |
| Churn Risk | `fldy4GIC8xDuPjS8y` | Risk signal; field appears as “Chrun riks” in Airtable |
| Task status | `fldaYegYsT0eA3NAK` | Account-level task summary, not task source of truth |
| Instance Link | `fldhc7LZLPB3b14fU` | Optional useful link |
| Assigned / Current Active CSM | `fldTQWeUcqj5HQoAH` | Mandatory assignment filter |

### Customer Tasks fields

| Field | Field ID | Use |
|---|---|---|
| Task Title | `fldbPKh3KNG9vbeXS` | Task name |
| Account | `fldMIaXj2PtqHkmNk` | Linked account |
| Owner | `fldVF6TQNNaPmv8qf` | Task owner |
| Status | `fldFbZYJvZUZmrGf2` | Task state |
| Priority | `fldGixN6MmFNAbTdG` | Task priority |
| Due Date | `fldm4uWSm23HVxZPC` | Due date |
| Customer Waiting? | `fldDXgxychawzNCZn` | Customer is waiting on Ranjodh/6sense/internal owner |
| Source Type | `fldRaStx1IoK4ooqv` | Source type |
| Source Date | `fldp9gpKEdSIywADe` | Source date |
| Source Summary | `fldKEtfu2JfA5VTKK` | Why the task exists |
| Source Detail Note | `fldo4zvmVhOXqed2p` | Linked Detailed Notes record |
| Completion Evidence | `fldsWVTfAAagVBTPT` | Evidence when done |
| Completed Date | `fldcOYlYd9dfmyl5K` | Completion date |
| Needs Review | `fld5SXTsSrQNl96t2` | Ambiguous/matching review flag |
| Last Updated From | `fldg0rM7UT6Jt148a` | Last workflow that updated task |

### Detailed Notes fields

| Field | Field ID | Use |
|---|---|---|
| Notes | `fldcfsEsMgFHoB8VH` | Detailed activity note |
| Title | `fldbf738tn5U18z7D` | Activity title / task heading |
| Account | `fldkJTerhbTfcObzR` | Linked account |
| Activity Type | `fldfi0aH7CncdNtb9` | Meeting Notes, Account Update, Support ticket, etc. |
| Date | `fld5Dd0gO8vHadVjl` | Activity date |
| Next Steps | `flduD4nsj6ZkEXmLZ` | Fallback source for open next actions |
| Customer Tasks | `fld5rGBShRUYrYIiR` | Linked task records |

---

## Customer Task Values

Open/actionable Customer Task statuses:

- `Open`
- `In Progress`
- `Waiting on Internal Team`
- `Waiting on Customer`
- `Blocked`
- `Needs Review`

Closed statuses:

- `Done`
- `Cancelled`

Priority values:

- `P1 - Must do`
- `P2 - Should do`
- `P3 - Monitor`

Owner values:

- `Ranjodh`
- `Customer`
- `AE / AM`
- `Support`
- `Internal Team`
- `Shared`
- `Unknown`

---

## Date Range Rules

If the user specifies a date range, use that range.

If the user says “last week,” use the previous Monday–Friday work week.

If the user does not specify a date range, default to the most recent Monday–Friday work week for activity review, while also using all open Customer Tasks regardless of source date.

Use Asia/Kolkata for interpreting “today,” “this week,” “last week,” and other relative dates.

When checking stale accounts or renewal focus, use account metadata beyond the target week because stale-account detection and renewal focus depend on older account state.

---

## Renewal Focus Window Rules

Every weekly command centre must include a Renewal Focus Window unless the user explicitly asks to skip renewals.

The renewal focus window is not the same as the weekly activity date range.

Default renewal focus window:

1. Today through the end of the current fiscal quarter.
2. The next fiscal quarter.
3. The first half of the following fiscal quarter.

Use the current date in Asia/Kolkata to determine the current quarter and forward-looking start date.

Past renewal dates are excluded by default. If Renewal Date is earlier than today in Asia/Kolkata, do not include the account in the Renewal Focus Window, even if the date is inside the current fiscal quarter.

Fiscal quarter mapping:

- Q1 = February 1 – April 30
- Q2 = May 1 – July 31
- Q3 = August 1 – October 31
- Q4 = November 1 – January 31

For the third quarter in the rolling window, include only the first half of that quarter. Use the first 45 days as the practical default.

Renewal focus filters:

- Assigned / Current Active CSM = `Ranjodh`
- Renewal Date is today or in the future
- Renewal Date falls between today and current-quarter end, inside the next fiscal quarter, or inside the first half of the following fiscal quarter
- Account has not already renewed, churned, or been fully offboarded unless the user explicitly asks to include past/closed renewals

Sort by Renewal Date ascending.

Urgency labels:

- `Immediate` = renewal today through next 30 days
- `Near-term` = renewal within 31–60 days
- `Upcoming` = renewal within 61–120 days
- `Later in window` = renewal more than 120 days away but still inside the renewal focus window

---

## Task Retrieval and Ranking

Pull Customer Tasks linked to Ranjodh-assigned accounts where Status is one of the open/actionable statuses.

Use Customer Tasks as the first source for:

- Customers Waiting on You
- Do First This Week
- Priority 1
- Priority 2
- Priority 3
- Renewal Focus Window pending task / next move

Use Activity notes and Detailed Notes only to:

- verify task context
- detect fallback tasks not yet converted into Customer Tasks
- identify completion signals that may require Update Notes or Task Centre to check off tasks
- support status/cadence/stale-account reasoning

If Activity notes or Detailed Notes show a task that is not yet in Customer Tasks, include it as a fallback item and label it `Not yet in Customer Tasks — capture via Update Notes/Task Centre`.

### Completed vs open items

For Customer Tasks, Status is authoritative.

For fallback notes-only items, do not keep an item open if later Activity notes/Detailed Notes say it was completed.

Completion signals include:

- “this was done”
- “sent the email”
- “meeting done”
- “support ticket resolved”
- “I reverted”
- “created a support case”
- “looped in the sales POC”
- “shared the docs”
- “confirmed with the customer”
- “scheduled the meeting”

If a Customer Task appears complete based on notes but Status is still active, include it in **Task Check-Off Review** instead of silently removing it.

---

## Customers Waiting on You

This is a high-importance section and must appear near the top, right after Interesting Observations when any items exist.

This section is the action-safety net for important follow-ups Ranjodh should not miss.

Primary inclusion logic:

- Customer Task is linked to a Ranjodh-assigned account.
- Status is open/actionable.
- Customer Waiting? is checked.
- Owner is `Ranjodh`, `Support`, `Internal Team`, `AE / AM`, `Shared`, or `Unknown`.

Also include accounts where Customer Tasks do not exist yet but Activity notes/Detailed Notes clearly show Ranjodh owes something, including:

- “I will send”
- “Ranjodh to follow up”
- “Need to share”
- “I’ll confirm”
- “Support ticket”
- “Waiting on internal team”
- “Need to schedule”
- “Need to send invite”
- “I’ll check”
- “I’ll get back”
- “Follow up with customer”
- “Send resources”
- “Share update”
- “Prepare”
- “Loop in”
- “Coordinate”
- “Confirm progress”
- “Track the support ticket”

For each item, include:

- Account
- Task / Waiting for
- Owner
- Status
- Due / timing

If no customers are waiting on Ranjodh, omit the section unless the user explicitly asks to show empty sections.

---

## Task Check-Off Review

Create this section when a Customer Task is active but Activity notes, Detailed Notes, or recent task source context suggest it may already be completed.

For each item, show:

- Account
- Task
- Current Status
- Possible completion evidence
- Suggested action

Do not auto-close tasks in Weekly Command Centre unless the user explicitly asks to close/check off tasks. Weekly Command Centre is primarily a review/reporting workflow; Update Notes and Task Centre are the preferred check-off engines.

---

## Status Hygiene

Before ranking priorities, check whether Engagement Status and Outreach Step appear missing, stale, or inconsistent.

Weekly Command Centre is read-only by default. Create **Recommended Status Updates** and do not write them automatically.

A recommendation is high-confidence only when:

- Account match is exact or strongly matched.
- Account passed the mandatory Assigned / Current Active CSM filter.
- New status is clearly supported by current Engagement Status, Outreach Step, legacy Stage, recent Activity notes, Detailed Notes, or active Customer Tasks.
- Update does not require guessing.
- Update does not downgrade a support/blocker or churn/offboarding state.

Never auto-increment outreach by time alone. A draft does not prove a follow-up was sent.

Apply recommended status changes only when the user explicitly asks to apply weekly updates. Before writing, restate the accounts and exact field changes being applied.

---

## Cadence Hygiene

Create **Cadence Hygiene** when cadence data is incomplete or inconsistent.

Only evaluate accounts assigned to Ranjodh by default.

Include an account when any of these are true:

- Engagement Status = `Cadence established` and Cadence Frequency is blank.
- Outreach Step = `Cadence active` and Cadence Frequency is blank.
- Meeting Sync established = `yes` and Cadence Frequency is blank.
- Engagement Status = `Cadence established` and Cadence Frequency = `TBD / not confirmed`.
- Outreach Step = `Cadence active` and Cadence Frequency = `TBD / not confirmed`.
- Meeting Sync established = `yes` and Engagement Status is not `Cadence established` or another active customer state.
- Cadence Frequency is populated but Engagement Status / Outreach Step do not show cadence or active relationship.
- Cadence Frequency = `Paused` but Outreach Step = `Cadence active`.

For each account, show:

- Account
- Current cadence state
- Cadence Frequency
- Issue
- Suggested action

Do not auto-update Cadence Frequency unless evidence is explicit and high-confidence.

---

## Priority Ranking

Rank accounts/tasks into three priority levels.

Do not force every account into a priority. Omit accounts with no meaningful task.

### Priority 1 — Must Do This Week

Use Priority 1 when any of these are true:

- Customer Task Priority = `P1 - Must do`.
- Customer Waiting? is checked and Owner is Ranjodh/Support/Internal Team/Shared/Unknown.
- Customer Task Status = `Blocked` or `Waiting on Internal Team` and customer is waiting.
- Engagement Status = `Support / blocker active`.
- Engagement Status = `Churn / offboarding` and action remains.
- Churn Risk is `Confirmed churn` or `Yellow`.
- Support blocker exists.
- Executive / CMO / decision-maker involvement exists.
- Salesforce stage progression is needed.
- Renewal or commercial process is close and not fully progressed.
- Renewal is today or later and within 120 days, and engagement is weak, blocked, or stale.
- Engagement Status = `Customer replied - scheduling` and customer is waiting for scheduling action.
- Engagement Status = `Multithread required` and renewal/risk is relevant.
- Outreach Step = `FU 3` or `FU 4` and renewal/risk is relevant.

### Priority 2 — Should Do This Week

Use Priority 2 when any of these are true:

- Customer Task Priority = `P2 - Should do`.
- Follow-up was promised last week.
- QBR / EBR prep is needed.
- Maturity model follow-up is needed.
- Open customer ask exists.
- Account has active momentum and should be moved forward.
- Meeting needs to be scheduled, prepped, or recapped.
- Engagement Status = `Intro sent - waiting` and no response has been logged.
- Engagement Status = `No response - follow-up needed` with Outreach Step = `FU 1` or `FU 2`.
- Engagement Status = `Connected - no cadence` and next cadence/action needs to be defined.
- Outreach Step = `Customer replied` or `Meeting scheduled`.
- Account-level Task status is `Open` or `yet to start` and linked Customer Tasks are active.

### Priority 3 — Monitor

Use Priority 3 when any of these are true:

- Customer Task Priority = `P3 - Monitor`.
- Waiting on customer response, but not urgent.
- Light-touch follow-up is needed.
- No urgent risk, but account still needs tracking.
- Renewal is not immediate but there is an open thread to watch.
- Account is quiet but not near renewal and no risk is confirmed.
- Engagement Status = `Cadence established` and next action is routine.
- Engagement Status = `Parked / no active outreach` and no renewal/risk action is present.

---

## Renewal Priority Overlay

Renewal proximity should boost priority only for future or same-day renewals.

Past renewal dates must not create renewal priority by default, even if they are inside the current fiscal quarter.

For Renewal Focus Window, Pending Task / Next Move should come from active Customer Tasks first.

If no active Customer Task exists, use Activity notes/Detailed Notes fallback. If no clear task exists, write:

```text
No clear pending task captured — confirm renewal plan / next step.
```

Move a renewal-focus account to Priority 1 if any of these are true:

- Renewal is today or later in the current fiscal quarter and any active Customer Task exists.
- Renewal is today or later and within 60 days, and any active Customer Task exists.
- Renewal is today or later and within 120 days, and Churn Risk is `Yellow` or `Confirmed churn`.
- Renewal is today or later and within 120 days, and Engagement Status is `Support / blocker active`, `Customer replied - scheduling`, `No response - follow-up needed`, `Multithread required`, or `Connected - no cadence`.
- Renewal is today or later and within 120 days, and no clear next step is captured.

Move a renewal-focus account to Priority 2 if it is inside the renewal focus window and has a pending task, missing cadence, stale note, unclear next step, or weak engagement signal, but does not meet Priority 1.

Keep a renewal-focus account in Monitor only if it is later in the window, cadence is established, outreach is cadence active, and no blocker, churn risk, customer wait, stale activity, or pending task exists.

---

## Stale / Risk Accounts to Watch

Only evaluate accounts assigned to Ranjodh by default.

Check:

- Engagement Status
- Outreach Step
- Last Activity Date
- latest parsed date inside Activity notes
- Renewal Date
- Churn Risk
- legacy Stage
- Activity notes
- active Customer Tasks
- whether there has been no meaningful update in 14, 21, or 30 days

Use this logic:

- If renewal is today or later and within 120 days, and there is no recent meaningful activity → urgent.
- If Renewal Date is earlier than today, do not treat it as a stale-renewal urgency driver by default.
- If churn risk is `Confirmed churn` or `Yellow` and there is no recent meaningful activity → urgent.
- If Engagement Status is `Intro sent - waiting` and no customer response is found → follow-up needed.
- If Engagement Status is `No response - follow-up needed` and Outreach Step is `FU 3` or `FU 4` → consider multithread or park.
- If Engagement Status is `Customer replied - scheduling` and there is no meeting scheduled → scheduling follow-up needed.
- If no activity in 30+ days but renewal is far away → medium priority.
- If stable and renewal is far away → monitor only.

For each stale account, include Account, Status, Signal, and Suggested move.

---

## Required Output Format — Compact Command Centre

Always use this compact format by default.

```text
Weekly Account Task Catch-Up
Date Range: [date range]

Interesting Observations
- [Only include 1–3 short observations if useful. Omit if not useful.]

Customers Waiting on You
| Account | Task / Waiting For | Owner | Status | Due / Timing |
|---|---|---|---|---|
| [Account] | [Customer Task Title or fallback owed item] | [Owner] | [Task Status or Engagement Status / Outreach Step] | [Due Date / today / this week / late] |

Task Check-Off Review
| Account | Task | Current Status | Possible Completion Evidence | Suggested Action |
|---|---|---|---|---|
| [Account] | [Task] | [Status] | [Evidence] | [Say naturally which task is complete; the task workflow resolves it internally] |

Recommended Status Updates
| Account | Engagement Status | Outreach Step | Reason |
|---|---|---|---|
| [Account] | [old → new or unchanged] | [old → new or unchanged] | [short reason] |

Status Ambiguities
| Account | Current Status | Possible Update | Why It Needs Review |
|---|---|---|---|
| [Account] | [current Engagement Status / Outreach Step] | [possible values] | [why the evidence is not conclusive] |

Cadence Hygiene
| Account | Current Cadence State | Cadence Frequency | Issue | Suggested Action |
|---|---|---|---|---|
| [Account] | [Engagement Status / Outreach Step / Meeting Sync established] | [Cadence Frequency or Blank] | [missing or inconsistent cadence detail] | [specific action] |

Renewal Focus Window
Scope: Today through Current Quarter + Next Quarter + First Half Following Quarter
| Rank | Window | Account | Renewal Date | Urgency | ACV | Status | Pending Task / Next Move |
|---|---|---|---:|---|---:|---|---|
| 1 | [Current Quarter / Next Quarter / First Half Following Quarter] | [Account] | [Renewal Date] | [Immediate/Near-term/Upcoming/Later in window] | [ACV] | [Engagement Status / Outreach Step] | [active Customer Task first, fallback next move second] |

Do First This Week
| Rank | Account | Status | Why it matters | Action |
|---|---|---|---|---|
| 1 | [Account] | [Task Status + Engagement Status / Outreach Step] | [short reason] | [clear action] |

Priority 1 — Must Do This Week

**[Account]**
- Status: [Task Status + Engagement Status / Outreach Step]
- Why: [one short reason]
- Do: [what needs to happen]
- Next step: [specific next step]

Priority 2 — Should Do This Week
| Account | Status | Action |
|---|---|---|
| [Account] | [Task Status + Engagement Status / Outreach Step] | [specific action] |

Priority 3 — Monitor
| Account | Status | What to monitor | Suggested move |
|---|---|---|---|
| [Account] | [Task Status + Engagement Status / Outreach Step] | [monitor signal] | [light next step] |

Stale / Risk Accounts to Watch
| Account | Status | Signal | Suggested move |
|---|---|---|---|
| [Account] | [Engagement Status / Outreach Step] | [stale/risk signal] | [action] |
```

If no customers are waiting on Ranjodh, omit **Customers Waiting on You** unless the user explicitly asks for empty sections.

If no check-off review items exist, omit **Task Check-Off Review**.

If no status recommendations exist, say: `No high-confidence Engagement Status or Outreach Step changes are recommended.`

If no status recommendations exist, omit **Recommended Status Updates**.

If no cadence hygiene issues exist, omit **Cadence Hygiene**.

If no accounts fall inside the renewal focus window after applying filters, say: `No renewing accounts found inside the renewal focus window.`

---

## Presentation Rules

- Compact format is mandatory unless the user explicitly asks for detailed mode.
- Start with Interesting Observations when useful.
- Then show Customers Waiting on You as the first action-oriented section when any items exist.
- Customers Waiting on You must not appear at the end.
- Then show Task Check-Off Review when applicable.
- Then show Recommended Status Updates and Status Ambiguities when applicable.
- Then show Cadence Hygiene when applicable.
- Then show Renewal Focus Window.
- Then show Do First This Week.
- Keep Do First This Week to the top 5–7 accounts/tasks.
- Show Engagement Status / Outreach Step and Customer Task Status in renewal and priority tables.
- Use tables for Customers Waiting on You, Task Check-Off Review, Recommended Status Updates, Status Ambiguities, Cadence Hygiene, Renewal Focus Window, Do First This Week, Priority 2, Priority 3, and Stale / Risk Accounts.
- Use short account blocks only for Priority 1.
- Do not include every possible task if it makes the output hard to read.
- Prioritize what the user should actually act on.

Maximum length guidance:

- Customers Waiting on You: max 8 rows
- Task Check-Off Review: max 8 rows
- Recommended Status Updates: max 8 rows
- Status Ambiguities: max 8 rows
- Cadence Hygiene: max 8 rows
- Renewal Focus Window: show all if readable; otherwise max 20 rows
- Do First This Week: max 7 rows
- Priority 1: max 6 accounts
- Priority 2: max 8 rows
- Priority 3: max 5 rows
- Stale / Risk Accounts: max 6 rows

If more items exist, add: `Additional lower-priority items omitted for readability.`

Detailed mode only when the user explicitly says: detailed, full details, expanded, show all accounts, include all fields, raw task list, full status hygiene, full renewal window, or full task list.

---

## What to Surface as Interesting

If useful, include 1–3 observations such as:

- Customers are waiting on Ranjodh across multiple accounts.
- Active Customer Tasks exist without due dates.
- Active Customer Tasks look completed but are not checked off yet.
- Engagement Status or Outreach Step is missing on high-priority accounts.
- Account has cadence active but Cadence Frequency is missing or `TBD / not confirmed`.
- Account is inside renewal focus window but has no clear pending task.
- Account has support blocker but no clear next update.
- Last Activity Date is recent but latest customer-facing activity is old.
- Outreach Step says `FU 3` or `FU 4`, but no multithread/park decision is captured.
- Non-Ranjodh accounts were excluded by the mandatory Assigned / Current Active CSM filter.

---

## Edge Cases

### No recent Airtable activity found

Say:

```text
No recent Airtable activity was found for the selected date range. I can still pull open Customer Tasks, stale accounts, and renewal/deal focus if account metadata is available.
```

### Activity exists but no action is needed

Say:

```text
Activity exists for the selected range, but no open Customer Tasks or account actions appear to require action this week.
```

### Account has mixed signals

Use the highest-priority signal. If Engagement Status conflicts with notes or Customer Tasks, include the account in Status Ambiguities unless the evidence is sufficient for a clear Recommended Status Update.

### Customer waiting on internal team

If the customer is waiting on an internal update, list it under Customers Waiting on You unless another owner is clearly responsible and no action is needed from Ranjodh.

### Churn Risk is unclear or stale

If Churn Risk is `Confirmed churn` but notes suggest the account is active, include the account and add: `Confirm whether Airtable Churn Risk is still accurate.` Do not automatically change Churn Risk.

### Engagement Status is support or churn

Do not override `Support / blocker active` or `Churn / offboarding` unless notes clearly confirm the blocker/churn/offboarding status is resolved.

### User asks for review only

If the user asks for review only, do not update Airtable. Provide recommendations in Recommended Status Updates, Cadence Hygiene, and Task Check-Off Review where relevant.

### User asks to check off / close tasks

If the user explicitly asks Weekly Command Centre to check off tasks, only close tasks when completion evidence is explicit. Otherwise mark `Needs Review` and explain what evidence is missing. Ranjodh can state naturally which task is complete; resolve it through Customer Task Centre without requiring a command or ID.

### No renewal-focus accounts found

If no accounts fall inside the renewal focus window after applying the mandatory assignment filter and future-date filter, say:

```text
No renewing accounts found inside the renewal focus window.
```

Then continue with Customers Waiting on You, Do First This Week, Priority 1, and remaining command centre sections if relevant.

### One-off meeting vs recurring cadence

Do not infer cadence from a single meeting being scheduled. A one-off meeting should remain `Meeting scheduled` unless notes explicitly confirm recurring cadence.

---

## Final Rule

This skill is for running the week. Customer Tasks is the primary execution source. It should help Ranjodh quickly decide which customers are waiting on him, what to do first, which tasks may need to be checked off, which future renewals need attention, which accounts are stale or risky, which accounts need outreach progression, and which cadence-active accounts need cleanup.

Always surface Customers Waiting on You near the top. Always apply the mandatory Ranjodh assignment filter unless explicitly overridden. Always exclude past renewal dates from the Renewal Focus Window by default.
