---
name: customer-task-centre
description: >
  Show, consolidate, sort, create, update, review, and check off customer/account work in
  Airtable Customer Tasks. Use for task centre, task command centre, all active tasks,
  account-grouped tasks, renewal-sorted tasks, detailed task audit, task history, task
  hygiene, check-off, cancellation, or reopening. This workflow is Airtable-only and must
  not query, display, deduplicate, or update Notion.
---

# Customer Task Centre — Airtable

## Purpose

Provide an exhaustive but compact operating view of Customer Tasks. The default view groups
tasks by account so fewer rows never means fewer tasks.

Use this skill to:

- show every active Customer Task linked to Ranjodh's assigned accounts, plus active unmapped
  records as a data-hygiene addendum
- consolidate tasks into one account row with Renewal Date
- sort accounts by urgency, renewal, due date, or priority
- switch to a one-row-per-task audit view
- create, update, complete, cancel, reopen, or review an Airtable task
- identify multi-account tasks, missing links, stale due dates, and other task hygiene gaps

Weekly Command Centre remains the wider weekly portfolio workflow. Update Notes and Meeting
Summarizer capture commitments from evidence. Customer Task Centre is the focused Airtable
execution queue.

---

## Shared Contracts

Before running this workflow, apply:

- `contracts/task-lifecycle.md` for task state, matching, deduplication, completion, and reopening.
- `contracts/write-safety.md` for read/write boundaries and draft-versus-sent rules.
- `contracts/portfolio-scope.md` for the default assigned-account scope.
- `contracts/untrusted-input.md` for emails, transcripts, pasted notes, and external content.
- `schema/airtable-schema-map.md` for current Airtable IDs and allowed values.

Do not load `schema/notion-task-map.md` for this workflow. If this skill conflicts with a
shared contract, the shared contract wins.

---

## Source Boundary

Customer Task Centre is Airtable-only.

- Read and write only Airtable Customer Tasks and the linked Airtable Accounts needed for scope,
  Renewal Date, and account-level Task status.
- Do not query, display, deduplicate, create, close, reopen, or update Notion tasks.
- Do not use a Notion item as evidence that an Airtable task exists or is complete.
- If the user asks for internal, manager, admin, enablement, AI/OKR, or project work, explain that
  it is outside this workflow and route the current request to Weekly Command Centre or another
  explicitly Notion-aware workflow. Ask one clarification only when customer versus internal scope
  is genuinely ambiguous.
- Missing Airtable evidence is an evidence gap. Do not expand automatically to another connector.

No change to this workflow deletes or alters existing Notion data.

---

## Triggers and Modes

Use this skill for:

- `/task manager`
- `/task centre`
- `/task center`
- `/task command centre`
- `/task command center`
- `/customer tasks`
- `/open customer tasks`
- `/task centre priority: P1`
- `/task centre compact`
- `/task centre detailed`
- `/task centre history`
- `/task centre sort: renewal`
- `/task centre sort: due`
- `/task centre sort: priority`
- `/task centre: [Account]`
- `/task hygiene`
- natural-language create, update, close, cancel, or reopen requests for Customer Tasks

Modes:

| Request | Behaviour |
|---|---|
| `/task centre` | Exhaustive active tasks, consolidated to one row per account |
| `/task centre compact` | Intentionally shortened priority subset; label the omitted count |
| `/task centre detailed` | Exhaustive active tasks, one row per task |
| `/task centre history` | Exhaustive detailed view including `Done` and `Cancelled` |
| `/task centre: [Account]` | Every active task for the named account |
| `/task centre priority: P1` | Detailed one-row-per-task view of every active P1 Customer Task |
| `sort: renewal` | Upcoming Renewal Date ascending; missing dates last |
| `sort: due` | Earliest active Due Date ascending; missing dates last |
| `sort: priority` | Accounts containing P1 tasks first, then P2, then P3 |

Default to Read. A natural-language task action authorizes only the matched Airtable change.

---

## Airtable Source of Truth

Base: Book of Business Management (`app6O8peF5ywLe1GM`)

Tables:

- Customer Tasks: `tblWUzwjGM4pwhAqR`
- Accounts: `tblr6UnvfaqfNvwyU`

Always use `schema/airtable-schema-map.md` for field IDs and allowed values.

Active task states:

- `Open`
- `In Progress`
- `Waiting on Internal Team`
- `Waiting on Customer`
- `Blocked`
- `Needs Review`

Closed states:

- `Done`
- `Cancelled`

---

## Retrieval and Join

For the default portfolio view:

1. Retrieve every Accounts record where Current Active CSM = `Ranjodh`. Follow pagination until
   the complete eligible-account set is loaded.
2. Retrieve every Customer Tasks record in every state. Follow pagination until the complete task
   table is loaded; do not discard records during retrieval.
3. Partition the complete task set into: active linked to an eligible account, closed linked to an
   eligible account, active unmapped, closed unmapped, and linked only to non-eligible accounts.
4. Join task Account record IDs to eligible Account record IDs. For a multi-account task, retrieve
   the name and Renewal Date of every linked Account record needed for display, including a linked
   non-eligible account; inclusion still requires at least one eligible account.
5. Read Renewal Date only from Accounts → Renewal Date. Never infer it from task dates, notes,
   Clari, or another field.
6. In default/detailed modes, display active eligible-linked tasks plus active unmapped hygiene
   records. In history mode, display eligible-linked and unmapped records in every state. Keep tasks
   linked only to non-eligible accounts outside the default scope.
7. Include in-scope tasks regardless of Owner, Priority, Due Date, Customer Waiting flag, or Needs
   Review.

Do not sample, cap, silently truncate, or replace the full roster with account summaries. If the
response would exceed a single message, calculate the complete ordered roster first, preserve one
global task-reference map, and continue as `Part X of Y — task references A–B`. Do not renumber
between parts. Provide the reconciliation only after the final part has been shown.

### Multi-account and unmapped tasks

- A task linked to exactly one eligible account belongs in that account row.
- A task linked to multiple accounts must appear once under `Multi-account / Needs Review`, with
  every linked account named. Do not repeat it in each account row.
- Number multi-account task references `M.1`, `M.2`, and so on. They do not contribute to any
  individual account row's Active, Overdue, or Next Due calculation.
- Place the Multi-account / Needs Review section after all regular account rows. Within it, apply
  the same task urgency order, then earliest linked Renewal Date, then Task Title.
- In renewal-sorted mode, keep multi-account tasks in the separate section after the account rows
  and order them by the earliest recorded Renewal Date among their eligible linked accounts.
- Show the Renewal Date for a multi-account task as `Account: Date` for every linked eligible
  account; use `Not recorded` for any missing date.
- An active task with a missing Account link is not silently assigned. Show it under `Unmapped /
  Data Hygiene` in every exhaustive mode, including the default view.
- Number unmapped task references `U.1`, `U.2`, and so on.
- Place `Unmapped / Data Hygiene` after `Multi-account / Needs Review`.
- A task linked only to non-eligible accounts is outside the default scope unless the user asks for
  that account or a broader scope.

---

## Default Consolidated View

Use one account row while preserving each underlying task:

| Account | Renewal Date | Active / Overdue | Next Due | All Active Tasks |
|---|---|---:|---|---|
| [Account] | [Date or Not recorded] | [count / count] | [earliest date or None] | **1.1** [Task] — [Priority] · [Status] · [Owner] · [Due timing] · [Customer waiting when true] |

Requirements:

- Number account rows `1`, `2`, `3` and task references `1.1`, `1.2`, `2.1`, and so on.
- Preserve the mapping from each displayed task reference to its Airtable record ID internally.
- Include every active task in the `All Active Tasks` cell; do not use `+N more`.
- Show `Not recorded` for a missing Renewal Date and `No due date` for a missing Due Date.
- Calculate Overdue only when Due Date is before today in Asia/Kolkata and the task is active.
- Calculate Next Due as the earliest nonblank active Due Date for that account.

Default account order:

1. Customer-waiting P1 or overdue P1
2. Other overdue work
3. Other P1 work
4. Earliest Next Due
5. Earliest Renewal Date
6. Account name

Within each account, order tasks by overdue/customer-waiting urgency, Priority, Due Date, then Task
Title.

---

## Detailed View

Use this for `/task centre detailed`, history, hygiene, or when the user asks to audit every row:

| # | Account | Renewal Date | Task | Owner | Status | Priority | Due Date | Customer Waiting? | Source Date |
|---:|---|---|---|---|---|---|---|---|---|

Show one task exactly once. Keep the same internal record-ID mapping used for task actions.

Use sequential integers `1`, `2`, `3`, and so on for ordinary single-account rows in detailed and
history modes. Reserve `M.n` for multi-account rows and `U.n` for unmapped rows. Account-based
references such as `1.1` apply only to the default consolidated account view.

Detailed row order:

1. Regular account task rows using the default account and within-account task order
2. Multi-account task rows using `M.n` references
3. Unmapped/data-hygiene rows using `U.n` references

---

## Completeness Checks

Before responding, reconcile:

`Eligible Active Airtable Tasks = Task References in Account Rows + Multi-account / Needs Review`

`Displayed Active Task Records = Eligible Active Airtable Tasks + Unmapped Active Hygiene Rows`

For detailed mode:

`Eligible Active Airtable Tasks = Eligible Detailed Active Rows`

`Displayed Detailed Rows = Eligible Detailed Active Rows + Unmapped Active Hygiene Rows`

For history mode, additionally report:

`Eligible Linked All-State Tasks = Active Linked Rows + Done Linked Rows + Cancelled Linked Rows`

`Displayed History Rows = Eligible Linked All-State Tasks + Unmapped All-State Hygiene Rows`

Unmapped rows are a data-hygiene addendum, not part of the assigned-account eligibility equation.

Also report:

- Eligible assigned accounts
- Accounts with active tasks
- Eligible active tasks
- Rows or task references displayed
- Multi-account tasks
- Unmapped/data-hygiene tasks displayed

If an equation fails, do not claim completeness. Continue retrieval, regenerate the display, or
place the unresolved records under Needs Review.

History row order:

1. Active rows using the default urgency ranking
2. `Done` and `Cancelled` rows by Completed Date descending; missing Completed Date last, then
   Source Date descending, then Task Title
3. Unmapped/data-hygiene rows last

---

## Compact Mode

Use compact mode only when explicitly requested.

- Show the highest-urgency accounts/tasks using the default ranking.
- State `Compact subset: X of Y active tasks shown`.
- Never label compact mode as all tasks or exhaustive.
- Preserve task reference mapping for displayed rows.

For `/task centre priority: P1`, filter only after the complete active set is partitioned and
reconciled. Use the detailed one-row-per-task layout and its ordering/numbering rules. Show:

- every P1 active task linked to at least one eligible account
- every P1 active unmapped task under `Unmapped / Data Hygiene`
- no non-P1 row

Reconcile:

`Displayed P1 Rows = Eligible-linked P1 Rows + Unmapped P1 Hygiene Rows`

State both denominators: `Eligible-linked P1: X of Y eligible active tasks` and `Unmapped P1: U of
V active unmapped tasks`. This is a scoped exhaustive view, not compact mode.

---

## Creating Tasks

Create only after an explicit request or an authorized source-ingestion workflow.

- Create customer/account commitments in Airtable Customer Tasks.
- Resolve the exact account before writing; ask one concise question if several records remain.
- Deduplicate against active and recently closed tasks before creation.
- Use Status `Open`, Priority `P2 - Should do`, Source Type `Manual`, Source Date = today in
  Asia/Kolkata, and Last Updated From = `Task Centre` unless stronger evidence supports other values.
- Do not create an Airtable task for a purely internal/project action unless the user explicitly
  wants it tracked as an account-linked Customer Task.

---

## Updating, Completing, Cancelling, and Reopening

Resolve the task from account, normalized title, state, timing, current conversation, and displayed
task reference. Use the Airtable record ID internally.

For updates:

- Change only the fields requested or supported by evidence.
- Re-fetch the Airtable record after the write.
- Recalculate Accounts → Task status after any Customer Task state change.

For completion:

- Apply `contracts/task-lifecycle.md` completion gates.
- Set Status = `Done`, Completed Date, concise Completion Evidence, Needs Review = unchecked, and
  Last Updated From = `Task Centre`.
- `Status = Needs Review` is an active lifecycle state. `Needs Review` (`fld5SXTsSrQNl96t2`) is a
  separate ambiguity checkbox; completion clears the checkbox while Status becomes `Done`.
- A draft or future promise is not completion.

For cancellation:

- Set Status = `Cancelled` and record the reason in Completion Evidence or Source Summary.
- Do not delete the task record.

For reopening:

- Resolve the existing `Done` or `Cancelled` record instead of creating a duplicate.
- Set the supported active state, clear Completed Date when supported, record the reopening reason,
  and set Last Updated From = `Task Centre`.

After any write, rerun the affected-scope completeness check.

---

## Account-Level Task Status

Customer Tasks remains authoritative. Update Accounts → Task status after a task state change:

- `closed` when no linked Customer Tasks remains active and the exact option exists.
- Otherwise, `yet to start` only when the exact option exists, every active task has Status `Open`,
  and no active task has Completion Evidence or Completed Date.
- Otherwise, `Open`, including when any task is In Progress, waiting, Blocked, or Needs Review.

Apply that precedence in order: `closed` → strict `yet to start` → `Open`.

Never create a select option implicitly.

---

## Companion Airtable Dashboard

The Airtable `Accounts` interface may contain a `Customer Task Centre` dashboard with:

- a Renewal Date-sorted Accounts list showing Task status and linked Customer Tasks
- an active Customer Tasks list grouped by Account
- filters/tabs for overdue, P1, customer-waiting, and Needs Review work

Treat the dashboard as an interactive convenience layer. The live Airtable tables and this skill's
retrieval/reconciliation rules remain authoritative. Do not assume the dashboard proves completeness;
reconcile from Accounts and Customer Tasks for every `/task centre` run.

---

## Confirmation Format

For display-only requests, do not include a write confirmation.

After a write, report:

```text
System Updated:
- Airtable

Tasks Updated:
- [Created / Updated / Done / Cancelled / Reopened]: [task] — [account] — [change]

Account Task Status:
- [Account]: [old value → new value, or No change]

Verification:
- [Re-fetched record and affected-scope reconciliation]
```

---

## What Not To Do

- Do not access Notion from Customer Task Centre.
- Do not silently truncate the default or detailed roster.
- Do not hide P2, P3, undated, customer-owned, Support-owned, waiting, blocked, or Needs Review tasks.
- Do not duplicate a multi-account task across account rows.
- Do not expose Airtable record IDs or require special record-ID commands.
- Do not delete Customer Task records in this workflow; use `Cancelled` to preserve history. A
  separate explicit destructive request requires fresh exact-record confirmation outside normal
  Task Centre actions.
- Do not close a task without explicit or contract-valid completion evidence.
- Do not update Engagement Status, Outreach Step, or other account workflow fields unless the user
  explicitly requests that separate change.
- Do not infer Renewal Date.

---

## Final Rule

Customer Task Centre is an Airtable-only, exhaustive execution queue. Consolidate by account without
losing task identity, show Renewal Date from the authoritative Accounts field, preserve every active
task exactly once, and make every write deliberate and verifiable.
