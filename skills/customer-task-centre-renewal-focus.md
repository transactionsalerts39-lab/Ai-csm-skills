---
name: customer-task-centre-renewal-focus
description: >
  Show a read-only Customer Task Centre renewal-focus view for every assigned account with a
  Renewal Date on or after today. Sort by Renewal Date ascending, then structured cadence status,
  calculate Last Verified Activity from canonical Airtable evidence, preserve every active Customer
  Task, and surface past-dated, missing-date, multi-account, and unmapped exceptions separately.
  This workflow is Airtable-only.
---

# Customer Task Centre — Renewal Focus View

## Purpose

Provide a dedicated renewal-oriented Customer Task Centre view without changing the normal
`/task centre` urgency ranking or the broader Weekly Command Centre renewal workflow.

Use this view to answer:

1. Which assigned accounts renew next, starting today?
2. When did substantive verified activity last occur on each account?
3. How many days have passed since that activity?
4. What is the structured cadence-field status for each upcoming renewal?
5. What active Customer Tasks are attached to each account?
6. Which upcoming-renewal accounts have no active task?
7. Which active tasks sit behind past-dated, missing, multi-account, or unmapped renewal data?

## Shared Sources and Contracts

Apply all of the following:

- `skills/customer-task-centre.md` for Airtable task identity, task references, active-state
  semantics, multi-account handling, unmapped handling, and completeness principles.
- `contracts/account-activity-recency.md` for Last Verified Activity calculation, evidence gates,
  source selection, Days Since Activity, and performance-safe retrieval.
- `contracts/task-lifecycle.md` for active task states and task matching.
- `contracts/tool-access-safety.md` before any connector call.
- `contracts/write-safety.md` for the read-only boundary.
- `contracts/portfolio-scope.md` for the default assigned-account scope and evidence boundary.
- `contracts/untrusted-input.md` for pasted or external evidence.
- `schema/airtable-schema-map.md` for Airtable field IDs and allowed values.

This specialized view overrides only the account inclusion, retrieval, ordering, and output rules
stated below. The base Customer Task Centre and shared contracts control everything else. If a
conflict remains, the shared contract wins.

## Triggers

Use this exact view for:

- `/task center renewal focus`
- `/task centre renewal focus`

These triggers are intentionally distinct from:

- `/task centre sort: renewal`, which remains the ordinary active-task roster sorted by renewal;
- plain `renewal focus`, which remains a Weekly Command Centre trigger.

## Mode and Source Boundary

This invocation is **Read only** and **Airtable-only**.

Allowed Airtable sources:

- Accounts for assignment, renewal, cadence fields, Activity notes fallback, and Last Activity Date
  as a retrieval signal only
- Customer Tasks for active work and dated task-source/completion evidence
- Detailed Notes for dated substantive account activity

Do not query Notion, Gmail, Calendar, Slack, or another connector. Do not update tasks, cadence
fields, renewal dates, activity fields, or account status during the view.

A later explicit natural-language task action may route to the normal Customer Task Centre write
workflow using the displayed task reference and internal Airtable record ID.

## Retrieval

Use a targeted retrieval plan rather than scanning unrelated account history.

1. Retrieve every Accounts record where Current Active CSM = `Ranjodh`, following all pages.
2. Read these account fields directly from Airtable:
   - Account Name
   - Renewal Date
   - Meeting Sync established
   - Cadence Frequency
   - Last Activity Date as a retrieval signal only
3. Use today in `Asia/Kolkata` as the inclusive renewal and activity boundary.
4. Partition assigned accounts into upcoming, past-dated, and missing-renewal-date sets before
   loading account-specific evidence.
5. Retrieve active Customer Tasks needed for task display and exception handling, following all
   pages. Include all active states from `contracts/task-lifecycle.md`.
6. Retrieve field-limited Customer Task activity-history candidates for assigned accounts as needed
   by `contracts/account-activity-recency.md`, including closed tasks when their Source Date or
   Completed Date may establish the latest qualifying activity.
7. Retrieve field-limited Detailed Notes only for assigned accounts in this view. Initial fields are
   Account, Date, Activity Type, and Title; lazily retrieve Notes / Next Steps only when needed to
   validate the latest candidate.
8. Inspect the full Accounts → Activity notes field only when the activity-recency contract's
   Last Activity Date retrieval gate says it may contain a newer qualifying dated entry.
9. Join Customer Tasks and Detailed Notes to Accounts by Airtable record ID.
10. Read Renewal Date only from Accounts → Renewal Date. Never infer it from task dates, notes,
    Clari, or another source.

If connector filtering cannot narrow a query by linked account IDs, retrieve the necessary pages and
filter to the verified assigned-account set immediately. Do not use unrelated records in the result.

## Last Verified Activity

Calculate every account's persisted activity result using `contracts/account-activity-recency.md`.

For each displayed account derive:

- Last Verified Activity Date
- Days Since Activity
- Activity Source
- short Activity Summary for internal interpretation when useful

For compact table display, render Last Verified Activity as:

`[date] · [source]`

Examples:

- `08 Aug 2026 · Detailed Note`
- `11 Aug 2026 · Task source: Email`
- `No verified dated activity found`

Do not display Accounts → Last Activity Date as if it were customer activity. It may be used only as
the contract-defined retrieval signal.

If no qualifying persisted evidence exists, Days Since Activity = `N/A`.

## Account Inclusion

### Main upcoming-renewal roster

Include every assigned account whose Renewal Date is today or later, whether it has:

- one or more active Customer Tasks; or
- no active Customer Tasks.

An account with no active task must still appear and show `No active Customer Tasks`.

### Exception sections

Do not mix these records into the main upcoming order:

1. `Past-dated renewal exceptions` — assigned accounts with Renewal Date before today that have one
   or more active Customer Tasks.
2. `Missing renewal date / data hygiene` — assigned accounts with no Renewal Date that have one or
   more active Customer Tasks.
3. `Multi-account / Needs Review` — active tasks linked to more than one account, following the base
   Customer Task Centre rules.
4. `Unmapped / Data Hygiene` — active tasks with no linked account, following the base rules.

Past-dated and missing-date account rows that are displayed must use the same Last Verified Activity
calculation as the main roster.

Also report counts of assigned past-dated and missing-date accounts that have no active tasks, but do
not add empty exception rows for them.

## Structured Cadence Status

This cadence classification uses only the two structured Airtable cadence fields below. The workflow
now retrieves Detailed Notes and Activity notes for **activity recency only**; do not reuse that
evidence to claim full cadence health inside this view.

Recurring frequencies:

- `Weekly`
- `Bi-weekly`
- `Monthly`
- `Quarterly`

Classify each account into exactly one **Cadence Field Status**:

### `Setup / restore required`

Use when both structured fields indicate that recurring cadence is not established:

- Meeting Sync established is blank, `no`, or otherwise not affirmative; and
- Cadence Frequency is blank, `TBD / not confirmed`, `Ad hoc / as needed`, or `Paused`.

### `Needs review`

Use when the two structured fields disagree:

- Meeting Sync established is affirmative but Cadence Frequency is not a recurring frequency; or
- Cadence Frequency is recurring but Meeting Sync established is blank, `no`, or unclear.

### `Confirmed recurring — fields only`

Use when:

- Meeting Sync established is affirmative; and
- Cadence Frequency is Weekly, Bi-weekly, Monthly, or Quarterly.

Do not label an account `Fully active` or `Slipping` in this view. Those labels require the evidence
checks in `skills/cadence-coverage-radar.md`.

## Ordering

Sort the main roster by this exact sequence:

1. Renewal Date ascending, starting with today.
2. For accounts sharing the same Renewal Date, Cadence Field Status in this action order:
   1. `Setup / restore required`
   2. `Needs review`
   3. `Confirmed recurring — fields only`
3. Account Name ascending.

Last Verified Activity and Days Since Activity are context columns only. They do **not** change the
renewal-focus sort unless the user explicitly asks for a different sort.

Within each account, order active tasks using the normal Customer Task Centre task urgency order:
overdue/customer-waiting urgency, Priority, Due Date, then Task Title.

Order past-dated renewal exceptions by Renewal Date descending, so the most recently elapsed date
appears first, then the same cadence-status order and Account Name. Order missing-date exceptions by
cadence-status order and Account Name.

For a multi-account task, keep the task in the separate base-skill section. Where at least one linked
eligible account has an upcoming Renewal Date, order the task by the earliest such date; otherwise
place it after the dated upcoming multi-account tasks and show each linked account's recorded date.

## Required Output

Use this main table:

| # | Account | Renewal Date | Days to Renewal | Last Verified Activity | Days Since Activity | Cadence Field Status | Meeting Sync | Cadence Frequency | Active / Overdue | Next Due | All Active Tasks |
|---:|---|---|---:|---|---:|---|---|---|---:|---|---|
| 1 | [Account] | [date] | [0 or positive integer] | [date · source or no-evidence label] | [integer or N/A] | [status] | [raw value or Not captured] | [raw value or Not captured] | [count / count] | [date or None] | [task references, or No active Customer Tasks] |

Requirements:

- Number main account rows `1`, `2`, `3`, and so on.
- Number tasks inside an account `1.1`, `1.2`, `2.1`, and so on.
- Preserve each task reference to Airtable record ID internally.
- Include every active task for the account; never use `+N more`.
- Calculate Days to Renewal as Renewal Date minus today in Asia/Kolkata.
- Calculate Last Verified Activity and Days Since Activity exactly from
  `contracts/account-activity-recency.md`.
- Calculate Overdue and Next Due using the normal Customer Task Centre rules.
- Show raw Meeting Sync established and Cadence Frequency beside the derived cadence status.
- Clearly label the report `Customer Task Centre — Renewal Focus` and include the as-of date.

When an account's activity result is `No verified dated activity found`, do not substitute the
record-modified date.

After the main table, show non-empty exception sections in this order:

1. Past-dated renewal exceptions
2. Missing renewal date / data hygiene
3. Multi-account / Needs Review
4. Unmapped / Data Hygiene

## Reconciliation

Before responding, verify:

`Upcoming Assigned Accounts = Upcoming Rows Displayed`

`Upcoming Activity Results = Upcoming Rows Displayed`

`Upcoming Active Task Records = Task References in Upcoming Account Rows + Upcoming Multi-account Task Records`

`Displayed Active Task Records = Upcoming Active Task Records + Past-dated Exception Task Records + Missing-date Exception Task Records + Other Multi-account Task Records + Unmapped Active Task Records`

Also report:

- Total assigned accounts
- Upcoming assigned accounts
- Upcoming accounts with verified dated activity
- Upcoming accounts with no verified dated activity
- Upcoming accounts with active tasks
- Upcoming accounts with no active tasks
- Upcoming active tasks
- Past-dated assigned accounts, split by with/without active tasks
- Missing-renewal-date assigned accounts, split by with/without active tasks
- Multi-account active tasks
- Unmapped active tasks
- Sort confirmation: `Renewal Date ascending from today → Cadence Field Status → Account Name`

If a reconciliation fails, do not claim completeness. Correct the roster or label the output
`Incomplete snapshot` with the specific missing or unresolved records.

## What Not To Do

- Do not route these exact triggers to Weekly Command Centre.
- Do not change the default `/task centre` ordering.
- Do not treat this view as `/task centre sort: renewal`.
- Do not exclude an upcoming-renewal account merely because it has no active task.
- Do not mix past-dated or missing renewal dates into the upcoming order.
- Do not infer Renewal Date.
- Do not use Accounts → Last Activity Date as Last Verified Activity.
- Do not use task Due Date, record timestamps, or future promises as activity.
- Do not infer full cadence health from activity-recency evidence.
- Do not access Notion or Gmail.
- Do not write to Airtable during this read-only view.

## Final Rule

For the exact renewal-focus triggers, show the complete assigned upcoming-renewal roster beginning
today, ordered first by Renewal Date and then by structured cadence-field action status. Calculate
the same defensible Last Verified Activity used by Account Follow-Up, preserve all active Customer
Tasks, expose no-task renewal accounts, and keep data-quality exceptions explicit.