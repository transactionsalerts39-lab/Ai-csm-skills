---
name: weekly-account-task-catch-up
description: >
  Pull Airtable account activity, account metadata, Engagement Status, and Outreach Step
  to create a focused weekly command center for accounts that need attention. Use this
  skill when the user asks for tasks to focus on this week, account catch-up, stale
  accounts, renewal/deal focus, customer follow-ups, accounts not touched in a while,
  or a weekly command centre based on Airtable. This version uses Engagement Status and
  Outreach Step as the primary account progression fields, includes a rolling renewal
  focus window covering the current quarter, next quarter, and first half of the following
  quarter, and can update those fields when high-confidence status hygiene changes are
  supported by Airtable activity. It also surfaces cadence hygiene issues where a
  cadence is active but Cadence Frequency is missing or inconsistent.
---

# Weekly Account Task Catch-Up / Weekly Command Centre — Engagement Status Enabled

## Purpose

Create a simple, action-focused weekly command center from Airtable.

This is not a manager recap and not a highlights/lowlights report. The goal is to help the CSM quickly understand:

- What needs attention this week
- Which accounts are at risk of being missed
- Which renewal/deal items need action
- Which renewals fall inside the focus window: current quarter, next quarter, and first half of the following quarter
- Which customers are waiting on the CSM
- Which accounts have gone stale or have not been meaningfully touched
- Which accounts need Engagement Status or Outreach Step cleanup
- Which cadence-active accounts are missing cadence frequency or have inconsistent cadence data

Use this skill when the user asks for:
- tasks for the week
- weekly command centre / weekly command center
- accounts to focus on
- deal focus areas
- stale accounts
- customer follow-ups
- accounts they have not touched in a while
- a weekly account catch-up from Airtable
- what needs attention from last week
- what to work on this week
- status hygiene for account outreach

---

# Airtable Source of Truth

Use Airtable as the primary source.

Base:
- **Book of Business Management**
- Base ID: `app6O8peF5ywLe1GM`

Primary table:
- **Accounts**
- Table ID: `tblr6UnvfaqfNvwyU`

Secondary table:
- **Detailed Notes**
- Table ID: `tblI5cCnIY63S6pZq`

Important:
- The **Accounts** table is the main working table.
- The user primarily updates this table, especially the **Activity notes** field.
- Treat **Activity notes**, **Engagement Status**, and **Outreach Step** as the richest sources for current account context.
- The **Detailed Notes** table is useful supporting context, especially for discrete notes and linked activities, but do not rely on it alone.

---

# Accounts Table — Key Fields

Use these fields from the Accounts table:

| Field | Field ID | Type | How to Use |
|---|---|---|---|
| Account Name | `fldOSLvopNOX6ae3Z` | singleLineText | Account name |
| Engagement Status | `fldyrxDGOzWF3c7wm` | singleSelect | Primary account relationship / engagement state |
| Outreach Step | `fldhX3nTqX4a2eKt8` | singleSelect | Primary outreach/follow-up sequence state |
| Meeting Sync established | `fld8kFpch7M4wGfpQ` | singleSelect | Indicates that a recurring customer sync/cadence has been confirmed. Use `yes` only when confirmed. |
| Cadence Frequency | `flddcR78KacwfLyk5` | singleSelect | Confirmed frequency of an established customer cadence. |
| Activity Log | `fldzKlsKjWYFGX4Q8` | linked records | Linked activity records; use as supporting detail |
| Activity notes | `flddz3lqUmEhLhmN5` | multilineText | Primary running notes field; parse dated notes and embedded task lists |
| Stage | `fldTYypHjPaFcatCi` | multipleSelects | Legacy fallback only; do not treat as primary |
| ACV | `fldjieKzPumeF6afD` | currency | Use to understand account value and priority |
| Renewal Date | `fldPmw5pHDNDgZYgA` | date | Use for renewal urgency |
| Last Activity Date | `fld2jD1HJm9RRwNBW` | lastModifiedTime | Use as an Airtable touch signal, but not proof of customer activity |
| Churn Risk | `fldy4GIC8xDuPjS8y` | singleSelect | Field is named “Chrun riks” in Airtable; values include Confirmed churn, Yellow, green |
| Task status | `fldaYegYsT0eA3NAK` | singleSelect | Values include Open, yet to start, closed |
| Instance Link | `fldhc7LZLPB3b14fU` | url | Optional useful reference for account work |
| Current Active CSM | `fldTQWeUcqj5HQoAH` | singleSelect | Use to filter to Ranjodh when needed |

## Primary status fields

Use **Engagement Status** and **Outreach Step** as the primary fields for weekly prioritization and progression. Use **Meeting Sync established** and **Cadence Frequency** as cadence-detail fields to verify whether an active cadence is fully captured.

Use the old **Stage** field only as fallback context or for legacy migration signals.

Do not update the old Stage field unless the user explicitly asks.

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

# Detailed Notes Table — Supporting Fields

Use these fields from the Detailed Notes table when available:

| Field | Field ID | Type | How to Use |
|---|---|---|---|
| Notes | `fldcfsEsMgFHoB8VH` | multilineText | Detailed activity note |
| Title | `fldbf738tn5U18z7D` | singleLineText | Activity title / task heading |
| Account | `fldkJTerhbTfcObzR` | linked account | Linked account |
| Activity Type | `fldfi0aH7CncdNtb9` | singleSelect | Activity type |
| Date | `fld5Dd0gO8vHadVjl` | date | Primary date field for structured activity records |
| Next Steps | `flduD4nsj6ZkEXmLZ` | multilineText | Strong source for tasks |
| Last activity date | `fldYG5croDSgYQmNN` | lastModifiedTime | Supporting update signal |

---

# Date Range Rules

If the user specifies a date range, use that range.

If the user says “last week,” use the previous Monday–Friday work week.

If the user does not specify a date range, default to the most recent Monday–Friday work week.

When pulling stale accounts, also use account metadata beyond the target week because stale-account detection depends on older activity.

Use Asia/Kolkata as the default timezone for interpreting “today,” “this week,” and similar relative dates.

---

# Renewal Focus Window Rules

Every weekly command centre must include a renewal focus window.

The renewal focus window is not the same as the weekly activity date range. The weekly date range controls recent activity review. The renewal focus window controls which upcoming renewals are surfaced first.

## Default renewal focus window

Unless the user specifies otherwise, include renewing accounts from:

1. The current fiscal quarter
2. The next fiscal quarter
3. The first half of the following fiscal quarter

This is a rolling forward-looking renewal window.

Use the current date in Asia/Kolkata to determine the current quarter.

## Fiscal quarter mapping

Use Ranjodh’s fiscal quarter definition:

- Q1 = February 1 – April 30
- Q2 = May 1 – July 31
- Q3 = August 1 – October 31
- Q4 = November 1 – January 31

If the renewal window crosses a calendar year, include those dates. Renewal work should be based on the rolling quarter window, not artificially capped at December 31.

## First half of the following fiscal quarter

For the third quarter in the rolling window, include only the first half of that quarter.

Use the midpoint of the fiscal quarter as the cutoff. If an exact midpoint is awkward, use the first 45 days of the quarter as a practical default.

Example:
If today is inside fiscal Q2, include:
- Current quarter: Q2
- Next quarter: Q3
- First half of following quarter: first half of Q4

## Renewal focus filters

Unless the user specifies otherwise, filter renewal focus accounts to:

- Current Active CSM = `Ranjodh`
- Renewal Date is within the renewal focus window
- Account has not already renewed, churned, or been fully offboarded unless action is still required

Sort by Renewal Date ascending.

Do not use top 10 for the full year by default. The renewal section should be based on the rolling quarter window.

---

# Important Interpretation Rules

## 1. Last Activity Date is not the same as last customer activity

The Accounts table **Last Activity Date** is a last-modified timestamp. It tells when the Airtable account record was last touched, but not always when the customer was actually contacted.

Use it as a useful signal, but also parse the **Activity notes** field for actual dated customer activity.

Example:
If Last Activity Date is recent but the latest dated note is old, say the record was updated recently but the customer touch may still be stale.

## 2. Activity notes are the richest source

The **Activity notes** field often contains:

- dated running notes
- meeting summaries
- “My action items”
- “Tasks identified”
- checkbox-style task lists
- support blockers
- customer asks
- renewal context
- stakeholder updates
- outreach notes
- customer replies
- meeting scheduling notes

Always parse this field carefully.

## 3. Engagement Status and Outreach Step are primary

Prioritize these fields over the old Stage field when classifying accounts.

Use **Stage** only to backfill or sanity-check when Engagement Status / Outreach Step are blank, stale, or contradictory.

## 4. Parse inline dates inside Activity notes

Look for date formats such as:

- `9 march`
- `10 march 26`
- `22 April`
- `27 april 26`
- `Apr 2, 2026`
- `23 : april`
- `8 april :`

If no year is given, assume the current year.

If a line has no date but follows a dated line, treat it as part of the previous dated note.

## 5. Extract embedded tasks

Look for task patterns such as:

- `My action items:`
- `Tasks identified in this meeting:`
- `Next step:`
- `Task:`
- `[ ]`
- `I need to`
- `I will`
- `I’ll`
- `Ranjodh to`
- `need to`
- `send`
- `follow up`
- `schedule`
- `prepare`
- `confirm`
- `loop in`
- `raise a support ticket`
- `track the support ticket`

Treat these as actionable items unless the note clearly says they are done.

## 6. Detect completed vs open items

If a later note says the item was completed, do not keep it as an open task.

Examples:
- “this was done”
- “sent the email”
- “meeting done”
- “support ticket resolved”
- “I reverted”
- “created a support case”
- “looped in the sales POC”

If the status is unclear, include it but phrase as “confirm whether this is complete.”

---

# Engagement Status / Outreach Step Interpretation

## Status-to-priority signals

| Engagement Status | Outreach Step | Default Interpretation |
|---|---|---|
| `Transitioned - not contacted` | `Not started` | Needs initial outreach, especially if newly transitioned |
| `Intro sent - waiting` | `Intro sent` | Needs follow-up if no customer response after reasonable time |
| `No response - follow-up needed` | `FU 1` / `FU 2` | Should follow up this week if still relevant |
| `No response - follow-up needed` | `FU 3` / `FU 4` | Stronger non-response signal; consider multithread or pause |
| `Customer replied - scheduling` | `Customer replied` | Customer is waiting or scheduling needs to move |
| `Meeting scheduled` | `Meeting scheduled` | Prep, attend, or confirm meeting details |
| `Connected - no cadence` | `Customer replied` | Relationship exists but cadence/plan may need definition |
| `Cadence established` | `Cadence active` | Monitor and keep next sync / action moving |
| `Multithread required` | `Multithread` | Needs stakeholder expansion or higher contact path |
| `Support / blocker active` | Any | Priority 1 if unresolved |
| `Parked / no active outreach` | `Parked` | Monitor unless renewal/risk makes action needed |
| `Churn / offboarding` | `Parked` | Priority 1 if any action remains; otherwise track offboarding |

## Legacy Stage fallback mapping

Use this only when Engagement Status or Outreach Step are blank, stale, or clearly inconsistent.

| Legacy Stage signal | Recommended Engagement Status | Recommended Outreach Step |
|---|---|---|
| `to reach out` | `Transitioned - not contacted` | `Not started` |
| `Email response pending` | `Intro sent - waiting` or `No response - follow-up needed` | Use latest note to infer `Intro sent`, `FU 1`, `FU 2`, `FU 3`, or `FU 4` |
| `FU 1` | `No response - follow-up needed` | `FU 1` |
| `fu 2` | `No response - follow-up needed` | `FU 2` |
| `fu 3` | `No response - follow-up needed` | `FU 3` |
| `fu 4` | `No response - follow-up needed` | `FU 4` |
| `Meeting scheduled` | `Meeting scheduled` | `Meeting scheduled` |
| `connected` | `Connected - no cadence` | `Customer replied` unless cadence is confirmed |
| `Support needed` | `Support / blocker active` | Keep current Outreach Step unless outreach changed |
| `Task pending` | Depends on note content | Depends on note content |

---

# Status Hygiene Update Rules

This skill may update Engagement Status and Outreach Step during the weekly command centre run when the update is high-confidence.

## High-confidence updates that can be applied

Apply the update when all are true:
- The account match is exact or strongly matched.
- The new status is clearly supported by current Engagement Status, Outreach Step, legacy Stage, and/or recent Activity notes.
- The update does not require guessing.
- The update does not downgrade a support/blocker or churn/offboarding state.

Examples:
- Engagement Status is blank and legacy Stage is `Meeting scheduled` → set `Meeting scheduled` / `Meeting scheduled`.
- Outreach Step is blank and latest note says “intro email sent” → set `Intro sent`.
- Engagement Status is `Intro sent - waiting` and latest note says “customer replied and asked for times” → set `Customer replied - scheduling` / `Customer replied`.
- Engagement Status is blank and latest note says “support ticket raised and customer is blocked” → set `Support / blocker active`.

## Review-only updates

Do not auto-update when:
- Evidence is old or contradictory.
- Notes mention a follow-up is needed but do not say it was sent.
- The account may be parked, but no explicit pause/no-active-outreach decision is captured.
- The update would downgrade `Support / blocker active` or `Churn / offboarding`.
- The account has multiple possible meanings and requires user judgment.

Show these in **Status Updates to Review**.

## Never auto-increment outreach by time alone

Do not move:
- `Intro sent` → `FU 1`
- `FU 1` → `FU 2`
- `FU 2` → `FU 3`
- `FU 3` → `FU 4`

unless Airtable notes clearly say the follow-up was actually sent.

The command centre can recommend “send FU 2,” but it should not mark `FU 2` until the follow-up has been logged.

---

# Workflow

## Step 1 — Pull Recent Airtable Data

Pull relevant account activity from Airtable for the target week or requested period.

Use:
1. Accounts table first
2. Detailed Notes table second

From the Accounts table, retrieve:
- Account Name
- Engagement Status
- Outreach Step
- Meeting Sync established
- Cadence Frequency
- Activity notes
- Activity Log
- Last Activity Date
- Renewal Date
- Stage as legacy fallback
- Churn Risk
- Task status
- ACV
- Instance Link if useful
- Current Active CSM if filtering to Ranjodh

From the Detailed Notes table, retrieve:
- Notes
- Title
- Account
- Activity Type
- Date
- Next Steps

Merge both sources by account.

If the user does not specify otherwise, focus on accounts where Current Active CSM is `Ranjodh` when that field is available.

---

## Step 2 — Apply Status Hygiene

Before ranking priorities, check whether Engagement Status and Outreach Step appear missing, stale, or inconsistent.

Create two groups:

1. **Status Updates Applied** — high-confidence changes that were updated in Airtable.
2. **Status Updates to Review** — recommended changes that should not be automatically written.

If the user asks for “review only,” “do not update Airtable,” or “no writes,” do not update fields. Put all status recommendations in **Status Updates to Review**.


## Step 2A — Apply Cadence Hygiene

After status hygiene and before ranking priorities, check whether cadence data is complete and consistent.

Create a section called **Cadence Hygiene** when any cadence issue exists.

Include an account in Cadence Hygiene when any of the following are true:

- Engagement Status = `Cadence established` and Cadence Frequency is blank.
- Outreach Step = `Cadence active` and Cadence Frequency is blank.
- Meeting Sync established = `yes` and Cadence Frequency is blank.
- Engagement Status = `Cadence established` and Cadence Frequency = `TBD / not confirmed`.
- Outreach Step = `Cadence active` and Cadence Frequency = `TBD / not confirmed`.
- Meeting Sync established = `yes` and Engagement Status is not `Cadence established` or another active customer state.
- Cadence Frequency is populated but Engagement Status / Outreach Step do not show cadence or an active relationship.
- Cadence Frequency = `Paused` but Outreach Step = `Cadence active`.

For each account, show:

- Account
- Current cadence state
- Cadence Frequency
- Issue
- Suggested action

Suggested actions should be practical, such as:

- Confirm whether cadence is weekly, bi-weekly, monthly, or quarterly.
- Confirm whether the cadence is still active.
- Update cadence frequency based on the recurring meeting schedule.
- Confirm whether the account should be `Connected - no cadence` instead.
- Confirm whether cadence should be marked `Paused`.

Do not auto-update Cadence Frequency during weekly command centre unless the source evidence is explicit and high-confidence. When evidence is ambiguous, put the account in Cadence Hygiene instead of updating it.

---

## Step 3 — Apply Priority Ranking

Rank accounts into three priority levels.

## Priority 1 — Must Do This Week

Use Priority 1 when any of the following are true:

- Engagement Status = `Support / blocker active`
- Engagement Status = `Churn / offboarding` and action remains
- Churn Risk is `Confirmed churn` or `Yellow`
- Renewal is upcoming or within 120 days and engagement is weak, blocked, or stale
- Customer is waiting on the CSM
- Support blocker exists
- Executive / CMO / decision-maker involvement exists
- Salesforce stage progression is needed
- Renewal or commercial process is close and not fully progressed
- Engagement Status = `Customer replied - scheduling` and the customer is waiting for scheduling action
- Engagement Status = `Multithread required` and renewal/risk is relevant
- Outreach Step = `FU 3` or `FU 4` and renewal/risk is relevant
- Legacy Stage includes `Support needed` and no newer status contradicts it
- Legacy Stage includes `Task pending` and the note indicates urgency

## Priority 2 — Should Do This Week

Use Priority 2 when any of the following are true:

- Follow-up was promised last week
- QBR / EBR prep is needed
- Maturity model follow-up is needed
- Open customer ask exists
- Account has active momentum and should be moved forward
- Meeting needs to be scheduled, prepped, or recapped
- Engagement Status = `Intro sent - waiting` and no response has been logged
- Engagement Status = `No response - follow-up needed` with Outreach Step = `FU 1` or `FU 2`
- Engagement Status = `Connected - no cadence` and a next cadence/action needs to be defined
- Outreach Step = `Customer replied`
- Outreach Step = `Meeting scheduled`
- Task status is `Open` or `yet to start`

## Priority 3 — Monitor

Use Priority 3 when any of the following are true:

- Waiting on customer response, but not urgent
- Light-touch follow-up is needed
- No urgent risk, but account still needs tracking
- Renewal is not immediate but there is some open thread to watch
- Account is quiet but not near renewal and no risk is confirmed
- Engagement Status = `Cadence established` and next action is routine
- Engagement Status = `Parked / no active outreach` and no renewal/risk action is present

Do not force every account into a priority. If there is no meaningful task, omit it.

## Renewal priority overlay

The renewal focus window should influence priority ranking.

A renewal inside the focus window should not automatically become Priority 1. However, renewal proximity should boost priority when there is an open task, weak engagement, risk, or no clear next step.

### Priority 1 boost

Move a renewal-focus account to Priority 1 if any of the following are true:

- Renewal is inside the current fiscal quarter and any task is open
- Renewal is within 60 days and any task is open
- Renewal is within 120 days and Churn Risk is `Yellow` or `Confirmed churn`
- Renewal is within 120 days and Engagement Status is:
  - `Support / blocker active`
  - `Customer replied - scheduling`
  - `No response - follow-up needed`
  - `Multithread required`
  - `Connected - no cadence`
- Renewal is within 120 days and no clear next step is captured
- Renewal is inside the current or next fiscal quarter and there is no meaningful recent activity

### Priority 2 boost

Move a renewal-focus account to Priority 2 if all of the following are true:

- Renewal is inside the renewal focus window
- The account is not Priority 1
- There is a pending follow-up, missing cadence, stale note, unclear next step, or weak engagement signal

### Monitor

Keep a renewal-focus account in monitor only if all are true:

- Renewal is in the later part of the focus window
- Engagement Status is `Cadence established`
- Outreach Step is `Cadence active`
- No blocker, churn risk, customer wait, stale activity, or pending task exists

---

# Step 4 — Identify Accounts Not Touched Recently

Create stale-account signals, but present them in the compact section called:

## Stale / Risk Accounts to Watch

Check:

- Engagement Status
- Outreach Step
- Last Activity Date
- latest parsed date inside Activity notes
- Renewal Date
- Churn Risk
- legacy Stage
- Activity notes
- whether there has been no meaningful update in 14, 21, or 30 days

## Stale-account logic

- If renewal is within 120 days and there is no recent meaningful activity → urgent.
- If churn risk is `Confirmed churn` or `Yellow` and there is no recent meaningful activity → urgent.
- If Engagement Status is `Intro sent - waiting` and no customer response is found → follow-up needed.
- If Engagement Status is `No response - follow-up needed` and Outreach Step is `FU 3` or `FU 4` → consider multithread or park.
- If Engagement Status is `Customer replied - scheduling` and there is no meeting scheduled → scheduling follow-up needed.
- If no activity in 30+ days but renewal is far away → medium priority.
- If stable and renewal is far away → monitor only.

For each stale account, include:

- Account name
- Signal
- Suggested move

Do not show long date diagnostics unless the user asks. If helpful, include dates inside the signal in plain English.

Example:
- Brightspot — Last meaningful activity Apr 1; renewal Jul 3; Engagement Status = Intro sent - waiting — Re-engage urgently.

---

# Step 5 — Add Renewal Focus Window

Create a mandatory section called:

## Renewal Focus Window

This section must appear before **Do First This Week** in the final output.

The purpose is to make sure upcoming renewals do not get buried behind general task noise.

## Renewal focus scope

Unless the user specifies otherwise, include accounts where:

- Current Active CSM = `Ranjodh`
- Renewal Date falls inside:
  - current fiscal quarter
  - next fiscal quarter
  - first half of the following fiscal quarter

Sort all renewal-focus accounts by Renewal Date ascending.

Do not default to top 10 for the full year. The renewal view should be based on the rolling quarter window.

## Renewal focus grouping

Group or label each account by renewal window:

- `Current Quarter`
- `Next Quarter`
- `First Half Following Quarter`

If space is tight, keep one combined table sorted by Renewal Date ascending and include a `Window` column.

## Pending task logic for renewal-focus accounts

For each renewal-focus account, identify the most important pending task or next move using this order:

1. Customer is waiting on Ranjodh
2. Open support blocker
3. Renewal / commercial next step
4. Meeting to schedule, prep, or recap
5. Follow-up promised but not completed
6. No cadence or weak engagement before renewal
7. No clear next step captured

If no task is found, write:

`No clear pending task captured — confirm renewal plan / next step.`

## Renewal urgency labels

Use this renewal urgency label:

- `Immediate` = renewal within 30 days
- `Near-term` = renewal within 31–60 days
- `Upcoming` = renewal within 61–120 days
- `Later in window` = renewal more than 120 days away but still inside the renewal focus window

## Renewal focus output limit

Default to showing all renewal-focus accounts if the list is readable.

If there are more than 20 accounts in the renewal focus window:
- Show all `Immediate` and `Near-term` accounts.
- Then show the earliest remaining accounts by Renewal Date until the table reaches 20 rows.
- Add: `Additional renewal-focus accounts omitted for readability. Ask for full renewal window to see all.`

## Renewal/deal progression signals

When using renewal/deal signals, consider:

- Renewal Date
- Renewal window label
- Engagement Status
- Outreach Step
- legacy Stage as fallback only
- Churn Risk
- ACV
- next stage/action needed
- missing customer engagement
- whether QBR/EBR is needed
- suggested next move

Example style:
- Ottimate — Renewal May 31, Current Quarter, Yellow risk, Engagement Status = Support / blocker active — align with Trey and move ROI deck forward.

---

# Step 6 — Identify Customers Waiting on Me

Create a section called:

## Customers Waiting on You

Look for notes that imply the CSM owes something.

Signals include:

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

Also include accounts where:
- Engagement Status = `Customer replied - scheduling`
- Outreach Step = `Customer replied`
- Engagement Status = `Support / blocker active`
- Engagement Status = `Intro sent - waiting` or `No response - follow-up needed` and the next action is owned by Ranjodh

For each item, include:

- Account
- Waiting for
- Timing

If the owner is unclear but the note implies the CSM owns it, assume the owner is Ranjodh but do not show an Owner column unless the user asks.

---

# Required Output Format — Compact Command Center

Always use this compact format by default.

The output should be easy to scan in 60 seconds.

Use markdown tables for sections that compare multiple accounts.

```text
Weekly Account Task Catch-Up
Date Range: [date range]

Interesting Observations
- [Only include 1–3 short observations if useful. Omit if not useful.]

Status Updates Applied
| Account | Engagement Status | Outreach Step | Reason |
|---|---|---|---|
| [Account] | [old → new or unchanged] | [old → new or unchanged] | [short reason] |

Status Updates to Review
| Account | Current Status | Recommended Update | Reason |
|---|---|---|---|
| [Account] | [current Engagement Status / Outreach Step] | [recommended values] | [why review is needed] |

Cadence Hygiene
| Account | Current Cadence State | Cadence Frequency | Issue | Suggested Action |
|---|---|---|---|---|
| [Account] | [Engagement Status / Outreach Step / Meeting Sync established] | [Cadence Frequency or Blank] | [missing or inconsistent cadence detail] | [specific action] |

Renewal Focus Window
Scope: Current Quarter + Next Quarter + First Half Following Quarter
| Rank | Window | Account | Renewal Date | Urgency | ACV | Status | Pending Task / Next Move |
|---|---|---|---:|---|---:|---|---|
| 1 | [Current Quarter / Next Quarter / First Half Following Quarter] | [Account] | [Renewal Date] | [Immediate/Near-term/Upcoming/Later in window] | [ACV] | [Engagement Status / Outreach Step] | [specific pending task or “No clear pending task captured — confirm renewal plan / next step.”] |

Do First This Week
| Rank | Account | Status | Why it matters | Action |
|---|---|---|---|---|
| 1 | [Account] | [Engagement Status / Outreach Step] | [short reason] | [clear action] |
| 2 | [Account] | [Engagement Status / Outreach Step] | [short reason] | [clear action] |

Priority 1 — Must Do This Week

**[Account]**
- Status: [Engagement Status / Outreach Step]
- Why: [one short reason]
- Do: [what needs to happen]
- Next step: [specific next step]

Priority 2 — Should Do This Week
| Account | Status | Action |
|---|---|---|
| [Account] | [Engagement Status / Outreach Step] | [specific action] |

Priority 3 — Monitor
| Account | Status | What to monitor | Suggested move |
|---|---|---|---|
| [Account] | [Engagement Status / Outreach Step] | [monitor signal] | [light next step] |

Stale / Risk Accounts to Watch
| Account | Status | Signal | Suggested move |
|---|---|---|---|
| [Account] | [Engagement Status / Outreach Step] | [stale/risk signal] | [action] |

Customers Waiting on You
| Account | Status | Waiting for | Timing |
|---|---|---|---|
| [Account] | [Engagement Status / Outreach Step] | [item owed] | [today/this week/late this week] |
```

If no status updates were applied, say:

```text
No high-confidence Engagement Status or Outreach Step updates were applied.
```

If no status review items exist, omit **Status Updates to Review**.

If no cadence hygiene issues exist, omit **Cadence Hygiene**.

If no accounts fall inside the renewal focus window, say:

```text
No renewing accounts found inside the renewal focus window.
```

---

# Presentation Rules

The compact format is mandatory unless the user explicitly asks for detailed mode.

## Default presentation behavior

- Start with **Cadence Hygiene** after any status-hygiene notes when cadence issues exist.
- Then show **Renewal Focus Window**.
- Then show **Do First This Week**.
- Keep **Do First This Week** to the top 5–7 accounts.
- Show Engagement Status / Outreach Step in renewal and priority tables.
- Use tables for:
  - Status Updates Applied
  - Status Updates to Review
  - Cadence Hygiene
  - Renewal Focus Window
  - Do First This Week
  - Priority 2
  - Priority 3
  - Stale / Risk Accounts
  - Customers Waiting on You
- Use short account blocks only for **Priority 1** because those need slightly more detail.
- Do not show long nested bullets for every section.
- Do not include every possible task if it makes the output hard to read.
- Prioritize what the user should actually act on.

## Maximum length guidance

- Status Updates Applied: max 8 rows
- Status Updates to Review: max 8 rows
- Cadence Hygiene: max 8 rows
- Renewal Focus Window: show all if readable; otherwise max 20 rows by default
- Do First This Week: max 7 rows
- Priority 1: max 6 accounts
- Priority 2: max 8 rows
- Priority 3: max 5 rows
- Stale / Risk Accounts: max 6 rows
- Customers Waiting on You: max 8 rows

If more items exist, add:
- “Additional lower-priority items omitted for readability.”

## Detailed mode

Only use the older, longer format if the user explicitly says:
- detailed
- full details
- expanded
- show all accounts
- include all fields
- raw task list
- full status hygiene
- full renewal window

---

# Output Style

- Keep it concise and practical.
- Use plain language.
- Use `-` bullets only inside short account blocks.
- Prioritize action over explanation.
- Do not include unnecessary background.
- Do not create a raw activity dump.
- Do not include stable accounts unless action is needed.
- Do not overcomplicate the output with extra sections.
- If a field name in Airtable is misspelled, output it with the clean business name. Example: `Chrun riks` → `Churn Risk`.

---

# What to Surface as “Interesting”

If relevant, briefly call out data-quality or workflow observations before the task list, such as:

- Engagement Status and Outreach Step are missing on high-priority accounts.
- Engagement Status appears inconsistent with recent activity.
- Outreach Step says `Intro sent`, but notes show the customer replied.
- Outreach Step says `FU 3` or `FU 4`, but no multithread/park decision is captured.
- Churn Risk appears inconsistent with recent activity.
- Last Activity Date is recent but latest customer-facing activity is old.
- Legacy Stage says `Task pending` but Task status says `closed`.
- Account has renewal soon but no meaningful recent activity.
- Account is inside the renewal focus window but has no clear pending task.
- Account is inside the current quarter renewal window but has weak engagement or no cadence.
- Account has cadence active but Cadence Frequency is missing or `TBD / not confirmed`.
- Meeting Sync established is `yes`, but Engagement Status / Outreach Step do not clearly show an active cadence.
- Account has support blocker but no clear next update.
- Account has strong customer momentum but the next task is not clearly captured.

Keep this section to 1–3 bullets maximum. If it does not help the user decide what to do, omit it.

---

# Edge Cases

## No recent Airtable activity found

Say:

```text
No recent Airtable activity was found for the selected date range. I can still pull stale accounts and renewal/deal focus if account metadata is available.
```

## Activity exists but no action is needed

Say:

```text
Activity exists for the selected range, but nothing appears to require action this week.
```

## Account has mixed signals

Use the highest-priority signal.

Example:
If an account has active engagement but also renewal risk within 120 days, classify it as Priority 1.

If Engagement Status conflicts with notes, include the account in Status Updates to Review unless the correct update is high-confidence.

## Customer waiting on internal team

If the customer is waiting on an internal update, still list it under Customers Waiting on You unless another owner is clearly responsible.

## Churn Risk is unclear or stale

If Churn Risk is `Confirmed churn` but notes suggest the account is active, include the account and add:

- Confirm whether Airtable Churn Risk is still accurate.

Do not automatically change Churn Risk.

## Engagement Status is support or churn

Do not override:
- `Support / blocker active`
- `Churn / offboarding`

unless the notes clearly confirm the blocker/churn/offboarding status is resolved.

## User asks for review only

If the user asks for review only, do not update Airtable. Provide recommendations in **Status Updates to Review** and **Cadence Hygiene** where relevant.

## No renewal-focus accounts found

If no accounts fall inside the renewal focus window, say:

```text
No renewing accounts found inside the renewal focus window.
```

Then continue with Do First This Week, Priority 1, and the remaining command centre sections.


## No cadence hygiene issues found

If all cadence-active accounts have a clear Cadence Frequency and there are no cadence inconsistencies, omit the Cadence Hygiene section.

## Cadence active but frequency missing

If an account has Engagement Status = `Cadence established`, Outreach Step = `Cadence active`, or Meeting Sync established = `yes`, but Cadence Frequency is blank or `TBD / not confirmed`, include it in Cadence Hygiene.

Do not treat the account as fully clean until Cadence Frequency is confirmed.

## One-off meeting vs recurring cadence

Do not infer cadence from a single meeting being scheduled. A one-off meeting should remain `Meeting scheduled` unless the notes explicitly confirm a recurring cadence.

## Too many renewal-focus accounts

If more than 20 accounts fall inside the renewal focus window, show the most urgent accounts first:

1. All Immediate and Near-term renewals
2. Earliest remaining renewals by Renewal Date ascending
3. Accounts with risk or missing next steps before healthy/stable accounts

Add:

```text
Additional renewal-focus accounts omitted for readability. Ask for full renewal window to see all.
```

---

# Final Rule

This skill is for running the week.

It should help Ranjodh quickly decide:

- what to do first
- which renewals are in the current quarter, next quarter, and first half of the following quarter
- which renewal-focus accounts have pending tasks or missing next steps
- which customers are waiting
- which accounts are stale or risky
- which accounts need outreach progression
- which cadence-active accounts are missing frequency or need cadence cleanup
- which Engagement Status / Outreach Step / Meeting Sync established / Cadence Frequency fields were updated or need review

The output should feel like a weekly command centre, not a long report.

Use Engagement Status and Outreach Step as the primary account progression fields. Use Meeting Sync established and Cadence Frequency as cadence-detail fields. Use the old Stage field only as fallback context. Do not update Stage unless explicitly asked.

Always include the renewal focus window before the general priority sections unless the user explicitly says to skip renewal focus.
