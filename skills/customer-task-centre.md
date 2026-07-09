---
name: customer-task-centre
description: >
  Show, create, update, review, and check off Customer Tasks in Airtable. Use this when the user
  asks for task centre, open tasks, customer tasks, tasks waiting on me, close/check off a task,
  mark tasks done, or review task hygiene. Customer Tasks is the durable execution tracker for
  CSM-owned follow-ups, customer-waiting items, support/internal follow-ups, and completion evidence.
---

# Customer Task Centre

## Purpose

Manage the Customer Tasks table directly.

Use this skill when Ranjodh wants to:

- view open tasks
- see customers waiting on him
- check off tasks
- mark tasks as done/cancelled/needs review
- create a manual task
- update owner, status, priority, due date, or customer-waiting flag
- review tasks that may already be completed
- inspect task hygiene across accounts

This is the focused task-management workflow. Weekly Command Centre summarizes and prioritizes tasks; Update Notes and Meeting Summarizer capture tasks from source content; Task Centre is where tasks can be deliberately reviewed and changed.

---

## Triggers

Use this skill for:

- `/task centre`
- `/task center`
- `/tasks`
- `/open tasks`
- `/customer tasks`
- `/tasks waiting on me`
- `/customers waiting on me`
- `/check off task`
- `/close task`
- `/mark task done`
- `/task hygiene`
- `/review tasks`
- `show my open tasks`
- `show tasks for [Account]`
- `mark [task] done for [Account]`

---

## Airtable Source of Truth

Base:

- Book of Business Management
- Base ID: `app6O8peF5ywLe1GM`

Tables:

- Customer Tasks: `tblWUzwjGM4pwhAqR`
- Accounts: `tblr6UnvfaqfNvwyU`
- Detailed Notes: `tblI5cCnIY63S6pZq`

Always use `schema/airtable-schema-map.md` for current field IDs and allowed values.

### Customer Tasks Fields

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
| Related Email / Meeting Summary | `fldhj5zbRG2dwT2sF` | Source excerpt/context |
| Completion Evidence | `fldsWVTfAAagVBTPT` | Evidence for closure |
| Completed Date | `fldcOYlYd9dfmyl5K` | Completion date |
| Needs Review | `fld5SXTsSrQNl96t2` | Ambiguous/review flag |
| Last Updated From | `fldg0rM7UT6Jt148a` | Always write `Task Centre` from this skill |

### Account Fields

- Account Name: `fldOSLvopNOX6ae3Z`
- Current Active CSM: `fldTQWeUcqj5HQoAH`
- Task status: `fldaYegYsT0eA3NAK`
- Customer Tasks linked records: `fldaetB5w1BersFeV`
- Engagement Status: `fldyrxDGOzWF3c7wm`
- Outreach Step: `fldhX3nTqX4a2eKt8`
- Renewal Date: `fldPmw5pHDNDgZYgA`
- Churn Risk: `fldy4GIC8xDuPjS8y`

---

## Default Scope

By default, show only Customer Tasks linked to accounts where Current Active CSM = `Ranjodh`.

Open/actionable statuses:

- `Open`
- `In Progress`
- `Waiting on Internal Team`
- `Waiting on Customer`
- `Blocked`
- `Needs Review`

Closed statuses:

- `Done`
- `Cancelled`

Default view:

- active tasks only
- Customer Waiting? checked first
- P1 before P2 before P3
- due soon before no due date
- account renewal/risk context included when helpful

If the user asks for a specific account, show tasks only for that account.

If the user asks for closed/completed history, include `Done` and `Cancelled`.

---

## Task Display Format

Use this by default:

```text
Customer Task Centre
Scope: [Ranjodh / specific account / requested filter]

Customers Waiting on You
| Account | Task | Owner | Status | Priority | Due Date | Notes |
|---|---|---|---|---|---:|---|
| [Account] | [Task Title] | [Owner] | [Status] | [Priority] | [Due Date / Blank] | [short source summary] |

Open Tasks
| Account | Task | Owner | Status | Priority | Due Date | Customer Waiting? |
|---|---|---|---|---|---:|---|
| [Account] | [Task Title] | [Owner] | [Status] | [Priority] | [Due Date / Blank] | [Yes/No] |

Needs Review
| Account | Task | Issue | Suggested Action |
|---|---|---|---|
| [Account] | [Task Title] | [duplicate/completion/owner ambiguity] | [what to confirm] |
```

Omit empty sections unless the user explicitly asks for all sections.

---

## Creating Tasks

Create a Customer Task only when the user explicitly asks to add/create a task or when task creation is part of a source-ingestion workflow that has clear task evidence.

Required fields for manual task creation:

- Task Title
- Account
- Owner if known, else `Unknown`
- Status, default `Open`
- Priority, default `P2 - Should do`
- Customer Waiting?, default based on wording
- Source Type, default `Manual`
- Source Date, default today in Asia/Kolkata
- Source Summary, concise reason the task exists
- Last Updated From = `Task Centre`

Before creating, search active tasks for the same account and similar title/owner to avoid duplicates.

If a similar task exists, update it instead of creating a duplicate unless the user explicitly says it is a separate task.

---

## Updating Tasks

When the user asks to change a task, identify the matching task by:

1. Account
2. Task Title / fuzzy task description
3. Owner/status/due date if needed

If one strong match exists, update it.

If multiple matches exist, ask one short clarification question unless the user gave enough context to proceed safely.

Allow updates to:

- Owner
- Status
- Priority
- Due Date
- Customer Waiting?
- Source Summary
- Completion Evidence
- Completed Date
- Needs Review
- Last Updated From

After task updates, recalculate Accounts → Task status for the linked account.

---

## Checking Off / Closing Tasks

When user asks to close, check off, or mark a task done:

1. Find the matching active task.
2. Confirm there is explicit completion evidence from the user or source content.
3. If evidence is explicit, update:
   - Status = `Done`
   - Completed Date = today in Asia/Kolkata unless a completion date is provided
   - Completion Evidence = user-provided evidence or concise summary
   - Needs Review = unchecked
   - Last Updated From = `Task Centre`
4. Recalculate Accounts → Task status.

If the user says “mark it done” and the task match is unambiguous, this is enough evidence to close the task. Use Completion Evidence = `Marked done by Ranjodh via Task Centre.`

If the task match is ambiguous, ask which task to close.

If completion evidence is weak and the user did not explicitly instruct closure, do not close it; mark Needs Review instead.

---

## Cancelling Tasks

Use Status = `Cancelled` when the user says the task is no longer needed, irrelevant, superseded, or should be removed from action tracking.

Set Completion Evidence / Source Summary to explain why it was cancelled.

Do not delete task records unless the user explicitly asks to delete records.

---

## Account-Level Task Status Rollup

After Customer Tasks changes, update Accounts → Task status as a summary signal:

- `Open` when one or more linked Customer Tasks are active.
- `yet to start` when tasks exist but none has started and the field value is available.
- `closed` when no linked Customer Tasks remain active and the field value is available.

If the exact select option is unavailable, do not force-create a new option.

Customer Tasks remains the source of truth.

---

## Confirmation Format

For display-only requests, do not include an update confirmation.

For changes, use:

```text
Updated Customer Tasks in Airtable.

Tasks Updated:
- Created: [task] — [account] — [owner] — [status] — [priority]
- Updated: [task] — [what changed]
- Done: [task] — [completion evidence]
- Cancelled: [task] — [reason]
- Needs Review: [task] — [why]

Account Task Status:
- [Account]: [old value → new value, or No change]
```

---

## What Not To Do

- Do not create duplicate tasks when an equivalent active task exists.
- Do not delete records unless explicitly asked.
- Do not close tasks unless completion is explicit or the user directly says to mark the task done.
- Do not update Engagement Status or Outreach Step from Task Centre unless the user explicitly asks; use Weekly Command Centre or Update Notes for broader account workflow changes.
- Do not include every closed task by default.
- Do not include non-Ranjodh accounts unless explicitly requested.

---

## Final Rule

Customer Tasks is the execution tracker. Use this skill to show, create, update, review, and check off tasks deliberately. Keep the output practical and action-focused.
