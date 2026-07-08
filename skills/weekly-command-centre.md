---
name: weekly-account-task-catch-up
description: >
  Pull Airtable account activity, account metadata, Engagement Status, Outreach Step,
  cadence fields, renewal dates, risk signals, and open follow-ups to create a focused
  weekly command centre for accounts that need attention. Use this skill when the user
  asks for weekly tasks, accounts to focus on, stale accounts, renewal focus, customer
  follow-ups, customers waiting on Ranjodh, or a weekly command centre based on Airtable.
  By default, only include accounts assigned to Ranjodh. The renewal focus window is
  forward-looking from today and excludes past renewal dates by default. Customers Waiting
  on You must appear near the top because these are important follow-ups that should not
  be missed.
---

# Weekly Account Task Catch-Up / Weekly Command Centre

## Purpose

Create a compact, action-focused weekly command centre from Airtable.

This is not a manager recap, highlights/lowlights report, raw Airtable dump, or full account dossier. The goal is to help Ranjodh quickly see:

- customers waiting on him
- accounts needing action this week
- future renewals inside the rolling focus window
- stale or risky accounts
- support blockers
- cadence hygiene issues
- status hygiene issues
- open follow-ups and next moves

The output should be easy to scan in about 60 seconds.

---

# Mandatory Assignment Filter

By default, include only Airtable Accounts records assigned to Ranjodh.

Apply this filter before any ranking, renewal, stale-account, cadence-hygiene, customer-waiting, or status-hygiene logic:

- Assigned / Current Active CSM field: `fldTQWeUcqj5HQoAH`
- Required default value: `Ranjodh`

Rules:

- Pull and include only Accounts where Assigned / Current Active CSM = `Ranjodh`.
- Do not include accounts assigned to another CSM.
- Do not include blank or unassigned accounts.
- Do not include non-Ranjodh accounts in Renewal Focus Window, Customers Waiting on You, Do First This Week, Priority sections, Stale / Risk Accounts, Status Hygiene, or Cadence Hygiene.
- When using Detailed Notes, use notes only for accounts that passed the Accounts-table assignment filter.
- If the user explicitly asks for another assignee, use that assignee instead.
- If the user explicitly asks for all accounts, state that the default Ranjodh assignment filter is being overridden for that run.

---

# Airtable Source of Truth

Use Airtable as the primary source.

Base:
- Book of Business Management
- Base ID: `app6O8peF5ywLe1GM`

Primary table:
- Accounts
- Table ID: `tblr6UnvfaqfNvwyU`

Secondary table:
- Detailed Notes
- Table ID: `tblI5cCnIY63S6pZq`

Important:

- Accounts is the main working table.
- The user primarily updates the Accounts table, especially `Activity notes`.
- Treat `Activity notes`, `Engagement Status`, and `Outreach Step` as the richest account-context sources.
- Detailed Notes is supporting context for discrete activities, meeting notes, and linked notes.
- Detailed Notes must not broaden scope beyond accounts assigned to Ranjodh unless the user explicitly overrides the assignment filter.

## Accounts Table — Key Fields

| Field | Field ID | Type | Use |
|---|---|---|---|
| Account Name | `fldOSLvopNOX6ae3Z` | singleLineText | Account name |
| Engagement Status | `fldyrxDGOzWF3c7wm` | singleSelect | Primary account relationship / engagement state |
| Outreach Step | `fldhX3nTqX4a2eKt8` | singleSelect | Primary outreach/follow-up step |
| Meeting Sync established | `fld8kFpch7M4wGfpQ` | singleSelect | Use `yes` only when recurring customer cadence is confirmed |
| Cadence Frequency | `flddcR78KacwfLyk5` | singleSelect | Confirmed cadence frequency |
| Activity Log | `fldzKlsKjWYFGX4Q8` | linked records | Supporting linked activity records |
| Activity notes | `flddz3lqUmEhLhmN5` | multilineText | Primary running account notes field |
| Stage | `fldTYypHjPaFcatCi` | multipleSelects | Legacy fallback only |
| ACV | `fldjieKzPumeF6afD` | currency | Prioritization and renewal context |
| Renewal Date | `fldPmw5pHDNDgZYgA` | date | Renewal urgency and focus window |
| Last Activity Date | `fld2jD1HJm9RRwNBW` | lastModifiedTime | Airtable record-touch signal, not proof of customer activity |
| Churn Risk | `fldy4GIC8xDuPjS8y` | singleSelect | Field appears as “Chrun riks” in Airtable; output as Churn Risk |
| Task status | `fldaYegYsT0eA3NAK` | singleSelect | Open / yet to start / closed support signal |
| Instance Link | `fldhc7LZLPB3b14fU` | url | Optional useful link |
| Assigned / Current Active CSM | `fldTQWeUcqj5HQoAH` | singleSelect | Mandatory assignment filter |

## Detailed Notes Table — Supporting Fields

| Field | Field ID | Type | Use |
|---|---|---|---|
| Notes | `fldcfsEsMgFHoB8VH` | multilineText | Detailed activity note |
| Title | `fldbf738tn5U18z7D` | singleLineText | Activity title / task heading |
| Account | `fldkJTerhbTfcObzR` | linked account | Linked account |
| Activity Type | `fldfi0aH7CncdNtb9` | singleSelect | Meeting Notes, Account Update, Support ticket, etc. |
| Date | `fld5Dd0gO8vHadVjl` | date | Primary date field for structured activity records |
| Next Steps | `flduD4nsj6ZkEXmLZ` | multilineText | Strong source for open next actions |
| Last activity date | `fldYG5croDSgYQmNN` | lastModifiedTime | Supporting update signal |

---

# Allowed Workflow Values

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

# Date Range Rules

If the user specifies a date range, use that range.

If the user says “last week,” use the previous Monday–Friday work week.

If the user does not specify a date range, default to the most recent Monday–Friday work week.

Use Asia/Kolkata for interpreting “today,” “this week,” “last week,” and other relative dates.

When checking stale accounts or renewal focus, also use account metadata beyond the target week because stale-account detection and renewal focus depend on older account state.

---

# Renewal Focus Window Rules

Every weekly command centre must include a Renewal Focus Window unless the user explicitly asks to skip renewals.

The renewal focus window is not the same as the weekly activity date range.

## Default renewal focus window

Unless the user specifies otherwise, include future/same-day renewals from:

1. Today through the end of the current fiscal quarter
2. The next fiscal quarter
3. The first half of the following fiscal quarter

Use the current date in Asia/Kolkata to determine the current quarter and forward-looking start date.

Past renewal dates are excluded by default. If Renewal Date is earlier than today in Asia/Kolkata, do not include the account in the Renewal Focus Window, even if the date is inside the current fiscal quarter.

Example: if today is July 9, 2026, a Renewal Date of July 8, 2026 is excluded by default, even though July 8 is inside fiscal Q2. A Renewal Date of July 9, 2026 or later can be included if it otherwise matches the focus window and assignment filters.

## Fiscal quarter mapping

Use Ranjodh’s fiscal quarter definition:

- Q1 = February 1 – April 30
- Q2 = May 1 – July 31
- Q3 = August 1 – October 31
- Q4 = November 1 – January 31

If the renewal window crosses a calendar year, include those dates.

For the third quarter in the rolling window, include only the first half of that quarter. Use the first 45 days as the practical default.

## Renewal focus filters

Unless the user specifies otherwise, include accounts where:

- Assigned / Current Active CSM = `Ranjodh`
- Renewal Date is today or in the future
- Renewal Date falls between today and current-quarter end, inside the next fiscal quarter, or inside the first half of the following fiscal quarter
- Account has not already renewed, churned, or been fully offboarded unless the user explicitly asks to include past/closed renewals

Sort by Renewal Date ascending.

Group or label each account by:

- `Current Quarter`
- `Next Quarter`
- `First Half Following Quarter`

For current-quarter accounts, include only dates from today through quarter-end.

Use these urgency labels:

- `Immediate` = renewal today through the next 30 days
- `Near-term` = renewal within 31–60 days
- `Upcoming` = renewal within 61–120 days
- `Later in window` = renewal more than 120 days away but still inside the renewal focus window

Do not use top 10 for the full year by default. The renewal section is based on the forward-looking rolling quarter window.

---

# Interpretation Rules

## Last Activity Date

Last Activity Date is a last-modified timestamp. It tells when the Airtable record was last touched, but not always when the customer was contacted.

Use it as a useful signal, but parse `Activity notes` and Detailed Notes dates for actual customer activity.

## Activity notes

Activity notes often contains dated running notes, meeting summaries, action items, support blockers, customer asks, renewal context, stakeholder updates, outreach notes, customer replies, and scheduling notes.

Always parse it carefully.

## Inline dates

Look for inline dates such as `9 March`, `10 March 26`, `22 April`, `27 April 26`, `Apr 2, 2026`, `23 : April`, and `8 April :`.

If no year is given, assume the current year.

If a line has no date but follows a dated line, treat it as part of the previous dated note.

## Completed vs open items

If a later note says the item was completed, do not keep it as open.

Completion signals include:

- “this was done”
- “sent the email”
- “meeting done”
- “support ticket resolved”
- “I reverted”
- “created a support case”
- “looped in the sales POC”

If status is unclear, include it as “confirm whether this is complete.”

---

# Customers Waiting on You

This is a high-importance section and must appear near the top of the output, right after Interesting Observations when any items exist.

This section is the action-safety net for important follow-ups Ranjodh should not miss.

Only include accounts assigned to Ranjodh by default.

Look for notes that imply Ranjodh owes something or the customer is waiting on him, including:

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
- Status
- Waiting for
- Timing

If no customers are waiting on Ranjodh, omit the section unless the user explicitly asks to show empty sections.

---

# Status Hygiene

Before ranking priorities, check whether Engagement Status and Outreach Step appear missing, stale, or inconsistent.

Create:

1. **Status Updates Applied** — high-confidence changes updated in Airtable.
2. **Status Updates to Review** — recommended changes that should not be automatically written.

Apply an update only when all are true:

- Account match is exact or strongly matched.
- Account passed the mandatory Assigned / Current Active CSM filter.
- New status is clearly supported by current Engagement Status, Outreach Step, legacy Stage, and/or recent Activity notes.
- Update does not require guessing.
- Update does not downgrade a support/blocker or churn/offboarding state.

Do not auto-update when evidence is old or contradictory, the account may be parked but no explicit pause is captured, the update would downgrade `Support / blocker active` or `Churn / offboarding`, or the account has multiple possible meanings.

Never auto-increment outreach by time alone. Do not move `Intro sent` → `FU 1`, `FU 1` → `FU 2`, `FU 2` → `FU 3`, or `FU 3` → `FU 4` unless Airtable notes clearly say the follow-up was actually sent.

If the user asks for review only, do not update fields.

---

# Cadence Hygiene

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

# Priority Ranking

Rank accounts into three priority levels.

Do not force every account into a priority. Omit accounts with no meaningful task.

## Priority 1 — Must Do This Week

Use Priority 1 when any of these are true:

- Engagement Status = `Support / blocker active`
- Engagement Status = `Churn / offboarding` and action remains
- Churn Risk is `Confirmed churn` or `Yellow`
- Customer is waiting on the CSM
- Support blocker exists
- Executive / CMO / decision-maker involvement exists
- Salesforce stage progression is needed
- Renewal or commercial process is close and not fully progressed
- Renewal is today or later and within 120 days, and engagement is weak, blocked, or stale
- Engagement Status = `Customer replied - scheduling` and customer is waiting for scheduling action
- Engagement Status = `Multithread required` and renewal/risk is relevant
- Outreach Step = `FU 3` or `FU 4` and renewal/risk is relevant
- Legacy Stage includes `Support needed` and no newer status contradicts it
- Legacy Stage includes `Task pending` and the note indicates urgency

## Priority 2 — Should Do This Week

Use Priority 2 when any of these are true:

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

Use Priority 3 when any of these are true:

- Waiting on customer response, but not urgent
- Light-touch follow-up is needed
- No urgent risk, but account still needs tracking
- Renewal is not immediate but there is some open thread to watch
- Account is quiet but not near renewal and no risk is confirmed
- Engagement Status = `Cadence established` and next action is routine
- Engagement Status = `Parked / no active outreach` and no renewal/risk action is present

---

# Renewal Priority Overlay

Renewal proximity should boost priority only for future or same-day renewals.

Past renewal dates must not create renewal priority by default, even if they are inside the current fiscal quarter.

Move a renewal-focus account to Priority 1 if any of these are true:

- Renewal is today or later in the current fiscal quarter and any task is open.
- Renewal is today or later and within 60 days, and any task is open.
- Renewal is today or later and within 120 days, and Churn Risk is `Yellow` or `Confirmed churn`.
- Renewal is today or later and within 120 days, and Engagement Status is `Support / blocker active`, `Customer replied - scheduling`, `No response - follow-up needed`, `Multithread required`, or `Connected - no cadence`.
- Renewal is today or later and within 120 days, and no clear next step is captured.
- Renewal is today or later inside the current or next fiscal quarter and there is no meaningful recent activity.

Move a renewal-focus account to Priority 2 if it is inside the renewal focus window and has a pending follow-up, missing cadence, stale note, unclear next step, or weak engagement signal, but does not meet Priority 1.

Keep a renewal-focus account in Monitor only if it is later in the window, cadence is established, outreach is cadence active, and no blocker, churn risk, customer wait, stale activity, or pending task exists.

---

# Stale / Risk Accounts to Watch

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

# Required Output Format — Compact Command Centre

Always use this compact format by default.

```text
Weekly Account Task Catch-Up
Date Range: [date range]

Interesting Observations
- [Only include 1–3 short observations if useful. Omit if not useful.]

Customers Waiting on You
| Account | Status | Waiting for | Timing |
|---|---|---|---|
| [Account] | [Engagement Status / Outreach Step] | [item owed] | [today/this week/late this week] |

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
Scope: Today through Current Quarter + Next Quarter + First Half Following Quarter
| Rank | Window | Account | Renewal Date | Urgency | ACV | Status | Pending Task / Next Move |
|---|---|---|---:|---|---:|---|---|
| 1 | [Current Quarter / Next Quarter / First Half Following Quarter] | [Account] | [Renewal Date] | [Immediate/Near-term/Upcoming/Later in window] | [ACV] | [Engagement Status / Outreach Step] | [specific pending task or “No clear pending task captured — confirm renewal plan / next step.”] |

Do First This Week
| Rank | Account | Status | Why it matters | Action |
|---|---|---|---|---|
| 1 | [Account] | [Engagement Status / Outreach Step] | [short reason] | [clear action] |

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
```

If no customers are waiting on Ranjodh, omit **Customers Waiting on You** unless the user explicitly asks for empty sections.

If no status updates were applied, say: `No high-confidence Engagement Status or Outreach Step updates were applied.`

If no status review items exist, omit **Status Updates to Review**.

If no cadence hygiene issues exist, omit **Cadence Hygiene**.

If no accounts fall inside the renewal focus window after applying filters, say: `No renewing accounts found inside the renewal focus window.`

---

# Presentation Rules

- Compact format is mandatory unless the user explicitly asks for detailed mode.
- Start with **Interesting Observations** when useful.
- Then show **Customers Waiting on You** as the first action-oriented section when any items exist.
- **Customers Waiting on You must not appear at the end by default.** It belongs near the top because these are owed follow-ups Ranjodh should not miss.
- Then show Status Updates Applied / Status Updates to Review when applicable.
- Then show **Cadence Hygiene** when applicable.
- Then show **Renewal Focus Window**.
- Then show **Do First This Week**.
- Keep **Do First This Week** to the top 5–7 accounts.
- Show Engagement Status / Outreach Step in renewal and priority tables.
- Use tables for Customers Waiting on You, Status Updates Applied, Status Updates to Review, Cadence Hygiene, Renewal Focus Window, Do First This Week, Priority 2, Priority 3, and Stale / Risk Accounts.
- Use short account blocks only for Priority 1.
- Do not include every possible task if it makes the output hard to read.
- Prioritize what the user should actually act on.

## Maximum length guidance

- Customers Waiting on You: max 8 rows
- Status Updates Applied: max 8 rows
- Status Updates to Review: max 8 rows
- Cadence Hygiene: max 8 rows
- Renewal Focus Window: show all if readable; otherwise max 20 rows
- Do First This Week: max 7 rows
- Priority 1: max 6 accounts
- Priority 2: max 8 rows
- Priority 3: max 5 rows
- Stale / Risk Accounts: max 6 rows

If more items exist, add: `Additional lower-priority items omitted for readability.`

## Detailed mode

Use detailed mode only if the user explicitly says: detailed, full details, expanded, show all accounts, include all fields, raw task list, full status hygiene, or full renewal window.

---

# What to Surface as Interesting

If useful, include 1–3 observations such as:

- Customers are waiting on Ranjodh across multiple accounts.
- Engagement Status or Outreach Step is missing on high-priority accounts.
- Account has cadence active but Cadence Frequency is missing or `TBD / not confirmed`.
- Account is inside renewal focus window but has no clear pending task.
- Account has support blocker but no clear next update.
- Last Activity Date is recent but latest customer-facing activity is old.
- Outreach Step says `FU 3` or `FU 4`, but no multithread/park decision is captured.
- Account has strong customer momentum but the next task is not clearly captured.
- Non-Ranjodh accounts were excluded by the mandatory Assigned / Current Active CSM filter.

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

Use the highest-priority signal. If Engagement Status conflicts with notes, include the account in Status Updates to Review unless the correct update is high-confidence.

## Customer waiting on internal team

If the customer is waiting on an internal update, still list it under Customers Waiting on You unless another owner is clearly responsible.

## Churn Risk is unclear or stale

If Churn Risk is `Confirmed churn` but notes suggest the account is active, include the account and add: `Confirm whether Airtable Churn Risk is still accurate.` Do not automatically change Churn Risk.

## Engagement Status is support or churn

Do not override `Support / blocker active` or `Churn / offboarding` unless notes clearly confirm the blocker/churn/offboarding status is resolved.

## User asks for review only

If the user asks for review only, do not update Airtable. Provide recommendations in Status Updates to Review and Cadence Hygiene where relevant.

## No renewal-focus accounts found

If no accounts fall inside the renewal focus window after applying the mandatory assignment filter and future-date filter, say:

```text
No renewing accounts found inside the renewal focus window.
```

Then continue with Customers Waiting on You, Do First This Week, Priority 1, and remaining command centre sections if relevant.

## No cadence hygiene issues found

If all cadence-active accounts have clear Cadence Frequency and there are no cadence inconsistencies, omit Cadence Hygiene.

## One-off meeting vs recurring cadence

Do not infer cadence from a single meeting being scheduled. A one-off meeting should remain `Meeting scheduled` unless notes explicitly confirm recurring cadence.

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

- which customers are waiting on him
- what to do first
- which future renewals are in the current quarter, next quarter, and first half of the following quarter
- which renewal-focus accounts have pending tasks or missing next steps
- which accounts are stale or risky
- which accounts need outreach progression
- which cadence-active accounts are missing frequency or need cadence cleanup
- which status fields were updated or need review

Customers Waiting on You must be surfaced near the top, not buried at the end.

Use Engagement Status and Outreach Step as the primary progression fields. Use Meeting Sync established and Cadence Frequency as cadence-detail fields. Use Stage only as fallback context. Do not update Stage unless explicitly asked.

Always apply the mandatory Ranjodh assignment filter unless the user explicitly overrides it.

Always exclude past renewal dates from the Renewal Focus Window by default.
