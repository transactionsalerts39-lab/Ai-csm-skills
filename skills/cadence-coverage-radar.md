---
name: cadence-coverage-radar
description: >
  Build a complete, read-only book-of-business cadence radar from Airtable. Use when
  Ranjodh asks for cadence coverage, cadence radar, book-of-business cadence, or cadence
  information for every account assigned to him. Show every assigned account, including
  fully active accounts, with mutually exclusive cadence health, supporting evidence,
  gaps, next actions, and active cadence-task status.
---

# Cadence Coverage Radar — Full Book of Business

## Purpose

Show the cadence position of the complete assigned book of business, not only exceptions.
The report must answer:

1. How many assigned accounts exist?
2. How many have verified fully active cadence?
3. What is the cadence state of every assigned account?
4. What gap and next action exists for every account that is not fully active?
5. Does each non-active account have an active cadence task?
6. Were all eligible accounts captured exactly once, with no missing, duplicate, or
   out-of-scope rows?

## Shared Contracts

Before running this workflow, apply:

- `contracts/portfolio-scope.md` for assignment scope, the cadence denominator, and evidence priority.
- `contracts/task-lifecycle.md` for active Customer Task states and task semantics.
- `contracts/write-safety.md` for the read-only boundary and Draft Is Not Sent.
- `contracts/untrusted-input.md` for emails, notes, transcripts, and other external evidence.
- `schema/airtable-schema-map.md` for current Airtable tables, fields, IDs, and allowed values.

If this skill conflicts with a shared contract, the shared contract wins.

## Mode and Source Boundary

This is a **Read** workflow. Never update Airtable, create or close tasks, change cadence
fields, or modify account assignment during the report.

Use Airtable only by default:

- Accounts is the complete roster and cadence-field source.
- Detailed Notes and dated Activity notes provide meeting and recurring-rhythm evidence.
- Customer Tasks provide open setup, scheduling, invite, reschedule, and restoration work.
- Do not query Notion. Notion is not authoritative for account cadence.
- Do not query email or calendar unless the user explicitly requests a separate cross-check.

If the user asks to apply a recommendation or create a missing cadence task, hand the
explicit action to Customer Task Centre or the appropriate write workflow.

## Scope and Completeness Gate

Default scope:

- Include every Accounts record where Current Active CSM = `Ranjodh`.
- Treat that complete assigned set as cadence eligible.
- Do not exclude an assigned account because it is inactive, parked, paused, ad hoc,
  churned, offboarding, low priority, missing data, or already fully active.
- Only a different or blank Current Active CSM value removes an account from the default scope.

If the user explicitly names another CSM, use every account assigned to that CSM. Within
this workflow, “all accounts” means every account in the selected assignment scope unless
the user explicitly requests all CSMs.

Retrieve every page of Accounts results before calculating coverage. Then retrieve relevant
Customer Tasks and meeting/cadence evidence only for accounts in that verified roster.

Use the Airtable Accounts record ID internally as the roster identity. Do not merge records
only because their display names are similar or reflect aliases. If two eligible records have
the same displayed Account Name, list both and flag the duplicate-name condition in Data
Quality Notes without exposing the record IDs.

If pagination, permissions, or a source failure prevents complete roster retrieval:

- label the output `Incomplete snapshot`;
- state how many assigned accounts were retrieved;
- state how many retrieved accounts were captured in the detailed lists;
- do not claim a definitive portfolio coverage percentage; and
- do not silently omit or estimate missing accounts.

## Retrieval

For every assigned account, retrieve:

- internal Accounts record ID for reconciliation only
- Account Name
- Current Active CSM
- Engagement Status
- Outreach Step
- Meeting Sync established
- Cadence Frequency
- Activity notes
- Last Activity Date as a retrieval signal only
- linked active Customer Tasks
- relevant Detailed Notes, including Date, Activity Type, Notes, and Next Steps

Use all active Customer Task states defined by `contracts/task-lifecycle.md`. A cadence task
is an active task whose action concerns recurring cadence, meeting scheduling, availability,
an invite, rescheduling, a missed/no-show sync, or cadence restoration.

Retrieve enough dated meeting evidence to evaluate the longest threshold below; use at
least the previous 180 days. Older evidence may support a current recurring arrangement
only when it is explicit and not contradicted by newer evidence.

## Evidence Rules

Use the evidence order in `contracts/portfolio-scope.md`.

- A confirmed customer meeting requires a dated meeting record or equally explicit trusted evidence.
- Last Activity Date alone is never a confirmed customer meeting.
- A draft, proposed time, or customer availability is not proof that a meeting or recurring invite was scheduled.
- One scheduled or completed meeting is not proof of recurring cadence.
- Recurring cadence evidence requires an explicit recurring rhythm, recurring invitation,
  or repeated meetings consistent with the recorded frequency.
- A future meeting is confirmed only when the evidence says it is scheduled or the invite was sent.
- Never invent a date, owner, frequency, task, or meeting status.
- When a value is absent, show `Not captured`, `No confirmed meeting`, or
  `No cadence task captured`, as appropriate.

## Cadence Health

Classify every eligible account into exactly one state:

- `Fully active`
  - Meeting Sync established = `yes`;
  - Cadence Frequency is Weekly, Bi-weekly, Monthly, or Quarterly;
  - trusted evidence supports a recurring customer rhythm;
  - no active setup, scheduling, invite, reschedule, or restoration obligation remains; and
  - the cadence is not slipping.

- `Slipping`
  - a recurring cadence is established; and
  - no future customer meeting is confirmed; and
  - the latest confirmed meeting exceeds the applicable threshold, or newer evidence
    shows repeated missed, cancelled, or unattended syncs.

- `Setup / restore required`
  - Meeting Sync is blank, no, or unclear;
  - Cadence Frequency is blank, `TBD / not confirmed`, `Ad hoc / as needed`, or `Paused`;
  - explicit recurring-rhythm evidence is missing; or
  - a recurring invite, next meeting, or cadence restoration obligation remains open
    without a material evidence conflict.

- `Needs review`
  - structured fields conflict with trusted notes or active tasks;
  - the account claims active cadence while an active task says cadence still must be established;
  - frequency, sync, and meeting evidence materially disagree; or
  - the available evidence is contradictory enough that choosing another state would require guessing.

Apply precedence in this order:

1. Needs review for material conflicts.
2. Slipping for an established cadence that fails reliability or recency.
3. Setup / restore required when any establishment gate is missing.
4. Fully active only when every positive gate passes.

Practical recency thresholds:

- Weekly: more than 14 days
- Bi-weekly: more than 21 days
- Monthly: more than 45 days
- Quarterly: more than 110 days

Do not use elapsed time alone to label `Ad hoc / as needed` as Slipping; classify it as
Setup / restore required while the account remains assigned.

## Reconciliation

Calculate:

- Total Eligible Accounts: unique assigned Accounts records retrieved after all pages
- Captured Accounts: unique eligible Accounts record IDs represented at least once in the
  detailed category lists
- Detailed Rows Displayed
- Accounts Classified Exactly Once
- Missing Accounts: eligible record IDs with no detailed row
- Duplicate Rows: repeated detailed appearances beyond the first appearance of an
  eligible record ID
- Out-of-Scope Rows
- Fully Active
- Setup / Restore Required
- Slipping
- Needs Review
- Coverage % = Fully Active / Eligible Accounts

Before responding, verify:

- Eligible Accounts = Fully Active + Setup / Restore Required + Slipping + Needs Review.
- Captured Accounts = Eligible Accounts.
- Detailed Rows Displayed = Eligible Accounts.
- Accounts Classified Exactly Once = Eligible Accounts.
- Missing Accounts = 0.
- Duplicate Rows = 0.
- Out-of-Scope Rows = 0.
- The continuous book-of-business row numbers run from 1 through Eligible Accounts with
  no skipped or repeated number.
- Every non-active row has a concrete gap and next action.
- Every non-active row shows an active cadence task or `No cadence task captured`.

Define a captured account as an eligible Accounts record that appears at least once in the
detailed category lists. Define classified exactly once separately: the eligible record
appears in one and only one detailed row. Use record IDs internally for both tests; do not
deduplicate on Account Name.

Show a `PASS` only when every check above succeeds. If any check fails, correct the roster
before responding. If correction is impossible because source access is incomplete, show
`INCOMPLETE` or `FAIL`, list every known missing, duplicate, or out-of-scope account by
name, and do not present the coverage percentage as definitive.

If Eligible Accounts = 0, show Coverage = `N/A`.

## Required Output

Use this format:

```text
Cadence Coverage Radar
As of: [date and time in Asia/Kolkata]
Scope: Every account where Current Active CSM = [assignee]

Portfolio Scorecard
| Total Eligible | Captured | Fully Active | Setup / Restore Required | Slipping | Needs Review | Coverage |
|---:|---:|---:|---:|---:|---:|---:|
| [count] | [count] | [count] | [count] | [count] | [count] | [percentage or N/A] |

Roster Integrity
| Total Eligible | Unique Captured | Detailed Rows | Classified Exactly Once | Missing | Duplicate Rows | Out of Scope | Result |
|---:|---:|---:|---:|---:|---:|---:|---|
| [count] | [count] | [count] | [count] | [count] | [count] | [count] | [PASS / INCOMPLETE / FAIL] |

Category Reconciliation
[Eligible] = [Fully Active] + [Setup / Restore Required] + [Slipping] + [Needs Review]

1. Fully Active — [count] of [eligible] ([share])
| BoB # | Account | Engagement / Lifecycle | Meeting Sync | Frequency | Last Confirmed Meeting | Next Confirmed Meeting | Recurring Evidence | Active Cadence Task | Evidence Gap | Required Next Action |
|---:|---|---|---|---|---|---|---|---|---|---|
| [continuous number] | [Account] | [status context] | [value] | [value] | [date or No confirmed meeting] | [date or No confirmed meeting] | [specific trusted evidence] | [task or No active cadence task required] | [No verified gap] | [maintain cadence action] |

2. Setup / Restore Required — [count] of [eligible] ([share])
[same detailed columns; one numbered row per account]

3. Slipping — [count] of [eligible] ([share])
[same detailed columns; one numbered row per account]

4. Needs Review — [count] of [eligible] ([share])
[same detailed columns; one numbered row per account]

Completeness Confirmation
- Accounts missing from detailed lists: [None, or every known account]
- Accounts appearing more than once: [None, or every account]
- Out-of-scope accounts included: [None, or every account]
- Final check: [PASS / INCOMPLETE / FAIL]
```

All four category sections are mandatory, even when a category contains zero accounts; in
that case show `None — 0 of [eligible]`. The detailed lists collectively form the complete
roster and are uncapped. Never replace them with only exceptions, a sample, “top accounts,”
or a statement that additional rows were omitted.

Use one continuous `BoB #` sequence across all category sections. The first displayed account
is 1 and the final displayed account equals Total Eligible. Do not reset numbering inside a
category. Every account row must show all detailed columns; never collapse a category into
an unnumbered comma-separated account list.

Display category sections in the fixed order shown above. Within Setup / Restore Required,
Slipping, and Needs Review, sort urgent or overdue cadence tasks first, then alphabetically.
Within Fully Active, sort alphabetically.

Calculate every category share against Total Eligible, not against only active or operational
accounts.

For a Fully Active row, show `No verified gap — maintain [frequency] cadence` and use
`No active cadence task required` when no cadence task exists.

After Completeness Confirmation, include `Data Quality Notes` only when source completeness,
duplicate display names, conflicting records, or uncaptured dates materially affect
interpretation.

## Final Rule

This workflow is the complete book-of-business cadence view. Every account in the selected
Current Active CSM assignment scope must appear with cadence information, including healthy
accounts. Every category must expose its full, numbered account list and the report must
prove that the total eligible roster was captured exactly once. Weekly Command Centre may
summarize cadence and emphasize exceptions; this skill must always provide the literal full
roster.
