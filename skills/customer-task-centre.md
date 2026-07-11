---
name: customer-task-centre
description: >
  Show, prioritize, create, update, review, and check off 6sense work across Airtable Customer Tasks
  and the canonical Notion Projects & tasks page. Use for task centre, task command centre, high-priority
  tasks, customer tasks, manager/internal work, check-off, or task hygiene. Preserve source ownership:
  Airtable for customer/account commitments and Notion for internal, manager, admin, AI/OKR, and project work.
---

# Task Centre — Unified 6sense Work

## Purpose

Manage one source-aware task view across Airtable and the 6sense Notion task page.

Use this skill when Ranjodh wants to:

- view a unified high-priority queue
- see open customer tasks and customers waiting on him
- see Notion high-priority, manager, admin, enablement, AI/OKR, or project tasks
- check off, cancel, reopen, create, or update a task
- review duplicates, stale items, missing due dates, or source hygiene

Weekly Command Centre summarizes the wider week and account portfolio. Update Notes and Meeting Summarizer capture customer commitments from evidence. Task Centre is the focused place to review and deliberately change tasks at their authoritative source.

---

## Shared Contracts

Before running this workflow, apply:

- `contracts/task-lifecycle.md` for task state, matching, deduplication, completion, and reopening.
- `contracts/write-safety.md` for read/draft/write boundaries and draft-versus-sent rules.
- `contracts/untrusted-input.md` for emails, transcripts, pasted notes, and external content.
- `schema/airtable-schema-map.md` for current Airtable IDs and allowed values.
- `schema/notion-task-map.md` for the canonical 6sense Notion pages, task sections, source ownership, deduplication, and write rules.

If this skill conflicts with a shared contract, the shared contract wins.

---

## Triggers

Use this skill for:

- `/task manager`
- `/task centre`
- `/task center`
- `/task command centre`
- `/task command center`
- `/high priority tasks`
- `/6sense tasks`
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

## Source Model

Build one view from two authoritative sources:

1. Airtable Customer Tasks for customer/account commitments, customer waiting, account-linked support/internal follow-ups, due dates, owners, lifecycle state, and completion evidence.
2. Notion `6 sense → Projects & tasks` for high-priority internal, manager, admin, enablement, AI/OKR, and project work.

Never silently mirror or bidirectionally sync tasks. Deduplicate cross-source customer items and keep the authoritative source visible.

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


### Notion Task Source

- Canonical page: `Projects & tasks`
- Page ID: `2e6ecca2-ea5e-8187-a2d4-d05b217c7ec3`
- Parent hub: `6 sense`
- Default sections: `High Priority 🚨`, `Customer Tasks`, `Manager tasks 🚨`, and `AI OKR Project Tasks`
- Open task: unchecked `[ ]`
- Completed task: checked `[x]`

Use `schema/notion-task-map.md` for exact URLs, exclusions, linked-page handling, source precedence, and writes. Do not read the unrelated personal `Tasks` page.

---

## Default Scope

Default to current open work from both sources:

- Airtable: Customer Tasks linked to accounts where Current Active CSM = `Ranjodh`
- Notion: open items on the canonical 6sense `Projects & tasks` page
- Exclude completed Notion checkboxes and Airtable `Done`/`Cancelled`
- Include backlog/dump items only when asked or when they carry explicit urgency
- Resolve Airtable row numbers to record IDs internally; resolve Notion items to the exact page and checkbox text internally
- Never require Ranjodh to remember a record ID or page ID

Unified ranking:

1. Overdue or P1 customer-waiting work
2. Open Notion `High Priority 🚨` work
3. Other P1/renewal-risk customer work
4. Due-soon manager/internal/project work
5. P2, P3, undated, and hygiene items

If a customer task appears in both systems, display one row with Airtable authoritative and Notion shown as a mirror/reference. If matching is uncertain, keep both under `Needs Source Review`.

---

## Task Display Format

Use this by default:

```text
Task Centre
Scope: 6sense work — Airtable + Notion

Unified High Priority
| # | Source | Account / Area | Task | Status | Priority | Due / Timing |
|---:|---|---|---|---|---|---|
| 1 | Airtable / Notion | [Account or work area] | [Task] | [Status/Open] | [Priority/High] | [Date/timing] |

Customers Waiting on You
| # | Account | Task | Owner | Status | Priority | Due Date |
|---:|---|---|---|---|---|---:|

Notion Work Queue
| # | Section | Task | State | Notes |
|---:|---|---|---|---|

Needs Review / Source Conflicts
| # | Task | Sources | Issue | Suggested Action |
|---:|---|---|---|---|
```

Omit empty sections. Label every row `Airtable` or `Notion`. Keep row numbering unique across the response.

---

## Creating Tasks

Choose the authoritative source before creating:

- Concrete customer/account commitment → Airtable Customer Tasks.
- High-priority internal, manager, admin, enablement, AI/OKR, or project action → canonical Notion `Projects & tasks` page.
- Ambiguous classification → ask one short question.

Create only after an explicit request or an authorized source-ingestion workflow. Deduplicate within the target source and across both sources first.

For Airtable creation, follow the existing schema and defaults: Status `Open`, Priority `P2 - Should do`, Source Type `Manual`, Source Date = today in Asia/Kolkata, and Last Updated From = `Task Centre`.

For Notion creation, insert an unchecked item into the most specific canonical section. Do not add it to `High Priority 🚨` unless the user marks it high priority or the evidence shows a real deadline/critical consequence. Re-fetch the page after writing.

---

## Updating Tasks

Resolve a task using source label, account/area, normalized task text, status, timing, current conversation, and displayed row number.

- Airtable-owned item → update the underlying Airtable record.
- Notion-owned item → update the exact checkbox/text on the canonical page or linked child page.
- Mirrored customer item → update Airtable only unless the user explicitly asks to sync Notion too.
- Multiple plausible matches or source ambiguity → ask one concise clarification.

Allow source-appropriate changes to owner, status, priority, due date, customer-waiting flag, source summary, completion evidence, review flag, Notion section, or checkbox state. After Airtable task changes, recalculate Accounts → Task status. After Notion changes, re-fetch and verify the exact item.

---

## Checking Off / Closing Tasks

When the user naturally says a task is done, sent, raised, resolved, closed, or should be checked off:

1. Resolve the displayed/source task.
2. Apply `contracts/task-lifecycle.md` completion gates.
3. Update only the authoritative source.
4. Verify the write.

For Airtable: set Status = `Done`, Completed Date, concise Completion Evidence, Needs Review = unchecked, Last Updated From = `Task Centre`, then recalculate account Task status.

For Notion: change the exact `[ ]` item to `[x]` on the canonical page or linked task page. Do not rewrite surrounding task content.

A direct unambiguous instruction such as `mark number 3 done` is sufficient. If a matching item exists in both systems but source ownership is unclear, ask which source to update rather than closing both.

---

## Cancelling Tasks

Use Status = `Cancelled` when the user says the task is no longer needed, irrelevant, superseded, or should be removed from action tracking.

Set Completion Evidence / Source Summary to explain why it was cancelled.

Do not delete task records unless the user explicitly asks to delete records.

---

## Reopening Tasks

Reopen a `Done` or `Cancelled` task only when Ranjodh explicitly asks or new evidence clearly proves more work remains.

When reopening:

- Resolve the existing closed record rather than creating a duplicate.
- Set the appropriate active status.
- Clear Completed Date when supported.
- Record why it was reopened.
- Set Last Updated From = `Task Centre`.
- Recalculate Accounts → Task status.

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
Updated task source(s).

Systems Updated:
- [Airtable / Notion]

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

- Do not create duplicate tasks when an equivalent active or explicitly reopened task exists.
- Do not expose Airtable record IDs or require special task commands.
- Do not delete records unless explicitly asked.
- Do not close tasks unless completion is explicit or the user directly says to mark the task done.
- Do not update Engagement Status or Outreach Step from Task Centre unless the user explicitly asks; use Weekly Command Centre or Update Notes for broader account workflow changes.
- Do not include every closed task by default.
- Do not include non-Ranjodh accounts unless explicitly requested.
- Do not use the unrelated personal Notion `Tasks` page.
- Do not auto-sync, auto-close, or create both Airtable and Notion copies.
- Do not treat Notion `Dump` as high priority without an urgency signal.

---

## Final Rule

Task Centre is the unified 6sense work queue. Airtable remains authoritative for customer/account execution; Notion remains authoritative for internal and project work. Rank both together, preserve source ownership, and keep every change deliberate and verifiable.
