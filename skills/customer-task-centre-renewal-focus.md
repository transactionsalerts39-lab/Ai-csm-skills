---
name: customer-task-centre-renewal-focus
description: >
  Show a read-only Customer Task Centre renewal-focus view for every assigned account with a
  Renewal Date on or after today. Sort by Renewal Date ascending, then structured cadence status,
  while preserving every active Airtable Customer Task and surfacing past-dated, missing-date,
  multi-account, and unmapped exceptions separately. This workflow is Airtable-only.
---

# Customer Task Centre — Renewal Focus View

## Purpose

Provide a dedicated renewal-oriented Customer Task Centre view without changing the normal
`/task centre` urgency ranking or the broader Weekly Command Centre renewal workflow.

Use this view to answer:

1. Which assigned accounts renew next, starting today?
2. What is the structured cadence-field status for each upcoming renewal?
3. What active Customer Tasks are attached to each account?
4. Which upcoming-renewal accounts have no active task?
5. Which active tasks sit behind past-dated, missing, multi-account, or unmapped renewal data?

## Shared Sources and Contracts

Apply all of the following:

- `skills/customer-task-centre.md` for Airtable retrieval, task identity, task references,
  active-state semantics, multi-account handling, unmapped handling, and completeness principles.
- `contracts/task-lifecycle.md` for active task states and task matching.
- `contracts/tool-access-safety.md` before any connector call.
- `contracts/write-safety.md` for the read-only boundary.
- `contracts/portfolio-scope.md` for the default assigned-account scope.
- `contracts/untrusted-input.md` for pasted or external evidence.
- `schema/airtable-schema-map.md` for Airtable field IDs and allowed values.

This specialized view overrides only the account inclusion and ordering rules stated below. The
base Customer Task Centre and shared contracts control everything else. If a conflict remains, the
shared contract wins.

## Triggers

Use this exact view for:

- `/task center renewal focus`
- `/task centre renewal focus`

These triggers are intentionally distinct from:

- `/task centre sort: renewal`, which remains the ordinary active-task roster sorted by renewal;
- plain `renewal focus`, which remains a Weekly Command Centre trigger.

## Mode and Source Boundary

This invocation is **Read only**.

- Use Airtable Accounts and Customer Tasks only.
- Do not query Notion, Gmail, Calendar, Slack, or another connector.
- Do not update tasks, cadence fields, renewal dates, or account status during the view.
- A later explicit natural-language task action may route to the normal Customer Task Centre write
  workflow using the displayed task reference and internal Airtable record ID.

## Retrieval

1. Retrieve every Accounts record where Current Active CSM = `Ranjodh`, following all pages.
2. Retrieve every Customer Tasks record, following all pages, then classify active states using
   `contracts/task-lifecycle.md`.
3. Join Customer Tasks to Accounts by Airtable record ID.
4. Read these account fields directly from Airtable:
   - Account Name
   - Renewal Date
   - Meeting Sync established
   - Cadence Frequency
5. Read Renewal Date only from Accounts → Renewal Date. Never infer it from task due dates, notes,
   Clari, or another source.
6. Use today in `Asia/Kolkata` as the inclusive lower boundary.

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

Also report counts of assigned past-dated and missing-date accounts that have no active tasks, but do
not add empty exception rows for them.

## Structured Cadence Status

This view uses only the two structured Airtable fields below. It does not claim the full
Cadence Coverage Radar health assessment, because it does not retrieve Detailed Notes, Activity
notes, meeting recency, or future-meeting evidence.

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

| # | Account | Renewal Date | Days to Renewal | Cadence Field Status | Meeting Sync | Cadence Frequency | Active / Overdue | Next Due | All Active Tasks |
|---:|---|---|---:|---|---|---|---:|---|---|
| 1 | [Account] | [date] | [0 or positive integer] | [status] | [raw value or Not captured] | [raw value or Not captured] | [count / count] | [date or None] | [task references, or No active Customer Tasks] |

Requirements:

- Number main account rows `1`, `2`, `3`, and so on.
- Number tasks inside an account `1.1`, `1.2`, `2.1`, and so on.
- Preserve each task reference to Airtable record ID internally.
- Include every active task for the account; never use `+N more`.
- Calculate Days to Renewal as Renewal Date minus today in Asia/Kolkata.
- Calculate Overdue and Next Due using the normal Customer Task Centre rules.
- Show raw Meeting Sync established and Cadence Frequency beside the derived status.
- Clearly label the report `Customer Task Centre — Renewal Focus` and include the as-of date.

After the main table, show non-empty exception sections in this order:

1. Past-dated renewal exceptions
2. Missing renewal date / data hygiene
3. Multi-account / Needs Review
4. Unmapped / Data Hygiene

## Reconciliation

Before responding, verify:

`Upcoming Assigned Accounts = Upcoming Rows Displayed`

`Upcoming Active Task Records = Task References in Upcoming Account Rows + Upcoming Multi-account Task Records`

`Displayed Active Task Records = Upcoming Active Task Records + Past-dated Exception Task Records + Missing-date Exception Task Records + Other Multi-account Task Records + Unmapped Active Task Records`

Also report:

- Total assigned accounts
- Upcoming assigned accounts
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
- Do not infer full cadence health from structured fields alone.
- Do not access Notion or Gmail.
- Do not write to Airtable during this read-only view.

## Final Rule

For the exact renewal-focus triggers, show the complete assigned upcoming-renewal roster beginning
today, ordered first by Renewal Date and then by structured cadence-field action status. Preserve all
active Customer Tasks, expose no-task renewal accounts, and keep data-quality exceptions explicit.