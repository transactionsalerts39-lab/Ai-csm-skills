# Account Activity Recency Contract — V1

This contract defines the canonical derived `Last Verified Activity` calculation for workflows that need to know when substantive activity last occurred on an account.

Apply this contract to Customer Task Centre — Renewal Focus and Account Follow-Up Builder. If either skill conflicts with this contract, this contract wins for activity-recency calculation and labeling.

## Purpose

Calculate a defensible account-activity date from dated evidence rather than treating Airtable record modification as customer movement.

The canonical persisted outputs are:

- `Last Verified Activity Date`
- `Days Since Activity`
- `Activity Source`
- `Activity Summary`

Use today in `Asia/Kolkata` for all date comparisons.

## Core Boundary

Accounts → `Last Activity Date` is a retrieval signal only. It is a `lastModifiedTime` field and is not proof that a customer interaction or substantive account event occurred.

Never use these as Last Verified Activity evidence by themselves:

- Accounts → Last Activity Date
- Detailed Notes → Last activity date
- Customer Task Due Date
- Airtable record creation or modification time
- Renewal Date
- Last Clari Sync Date
- task creation alone
- a task status change alone without event evidence
- AI-generated account headline or summary fields
- automation touches, rollups, metadata cleanup, CRM hygiene, or field edits
- drafts, plans, proposals, future promises, or future meetings that have not occurred

## Qualifying Activity

A qualifying activity is a dated event on or before today that demonstrates actual customer interaction or substantive movement on the account, such as:

- a customer meeting, call, training, or working session that occurred
- an inbound or outbound customer email or transcript event
- a support case being raised, materially updated, or resolved for the customer
- a deliverable, report, resource, invite, or requested information actually sent or shared
- a customer decision, confirmation, renewal/procurement movement, or meaningful response
- an account/product configuration or enablement action actually completed

Purely internal planning, task management, research, administrative work, or future intent does not qualify unless the evidence also proves a substantive account event occurred.

## Persisted Airtable Evidence Sources

Build candidate events only from the named account and use this evidence order.

### 1. Detailed Notes → Date

A Detailed Notes record qualifies when:

- it is linked to the account;
- `Date` is populated and on or before today; and
- Activity Type, Title, Notes, or Next Steps establishes that the record documents a qualifying activity rather than only internal planning or a placeholder.

Use the Detailed Notes `Date`, not its last-modified timestamp.

### 2. Customer Tasks → Completed Date

A Customer Task completion qualifies when:

- it is linked to the account;
- `Completed Date` is populated and on or before today; and
- Completion Evidence or trusted source context proves the completed action itself was a qualifying account activity.

`Done` plus a Completed Date with missing, generic, contradictory, or purely administrative evidence is not sufficient by itself.

### 3. Customer Tasks → Source Date

A Customer Task source qualifies when:

- it is linked to the account;
- `Source Date` is populated and on or before today; and
- the source represents a qualifying account event.

`Email`, `Meeting`, `Transcript`, and `Support` are normally eligible source types when their source context supports the event.

`Slack / Internal`, `Manual`, and `Other` do not qualify by default. They may qualify only when Source Summary, Related Email / Meeting Summary, or other trusted evidence explicitly records a completed customer-facing or account-impacting event rather than internal planning.

### 4. Dated Accounts → Activity notes entry

An Activity notes entry qualifies only when:

- the entry contains an explicit event date on or before today; and
- the entry clearly describes a qualifying account activity.

Do not use an undated note, relative wording that cannot be resolved safely, or the containing record's Last Activity Date as a substitute date.

## Candidate Selection

1. Build all qualifying persisted Airtable candidates for the account.
2. Exclude future-dated events. If a future date appears to describe completed activity, flag it as `Needs review`; do not calculate a negative Days Since Activity.
3. Select the latest calendar date.
4. When multiple qualifying candidates share that date, use this tie-break order for the displayed source:
   1. Detailed Notes
   2. Customer Task Completed Date
   3. Customer Task Source Date
   4. Dated Activity notes entry
5. If multiple same-tier events share the winning date, prefer the record with the clearest direct evidence. If several materially distinct qualifying events occurred that day, use `Multiple qualifying activities` as the summary rather than inventing a single winner.

Calculate:

`Days Since Activity = today in Asia/Kolkata - Last Verified Activity Date`

If the winning date is today, Days Since Activity = `0`.

If no qualifying candidate exists, return:

- Last Verified Activity Date = `No verified dated activity found`
- Days Since Activity = `N/A`
- Activity Source = `None verified`

Do not fall back to Accounts → Last Activity Date.

## Current-Conversation Overlay

The persisted calculation above must remain Airtable-derived so Renewal Focus and Account Follow-Up can agree on the same stored activity date.

Account Follow-Up Builder may additionally surface `Newer unlogged activity` when the current conversation contains trusted, explicit evidence of a qualifying event newer than the persisted Airtable result.

Rules:

- Keep the Airtable-derived Last Verified Activity unchanged and clearly labeled.
- Show the newer event separately as `Current conversation — not yet logged in Airtable`.
- Use an explicit event date when supplied. If the user states in the current turn that they just completed the action, today in Asia/Kolkata may be used.
- A draft, future promise, quoted history, or uncertain statement is not newer unlogged activity.
- Do not write or backfill Airtable merely because newer conversation evidence exists.

## Activity Summary

Keep the summary short and factual.

Preferred sources:

- Detailed Notes Title plus Activity Type
- completed task title plus concise Completion Evidence
- source task title plus concise Source Summary / Related Email or Meeting Summary
- concise dated Activity-notes entry

Do not expose private strategy or unsupported interpretation in customer-facing copy. The activity check itself is internal workflow context unless the user explicitly asks to use it in a draft.

## Performance-Safe Retrieval

Use the smallest sufficient Airtable reads.

### First pass

For Accounts retrieve only the fields needed for the routed workflow plus `Last Activity Date` as a retrieval signal.

For Detailed Notes retrieve initially only:

- Account
- Date
- Activity Type
- Title

For Customer Tasks reuse already-retrieved task fields where possible and retrieve only the activity-candidate fields needed:

- Account
- Source Type
- Source Date
- Completed Date
- Task Title

### Lazy evidence expansion

Retrieve longer text only when needed to validate the latest candidate or resolve ambiguity:

- Detailed Notes → Notes / Next Steps
- Customer Tasks → Completion Evidence
- Customer Tasks → Source Summary
- Customer Tasks → Related Email / Meeting Summary

Use Accounts → Last Activity Date only as a retrieval gate for Activity notes. If no structured candidate exists, inspect Activity notes. If a structured candidate exists and Last Activity Date is later than that candidate date, inspect Activity notes for a newer explicitly dated qualifying entry. If Last Activity Date is not later than the structured candidate, do not read the full Activity notes merely for recency.

This gate optimizes retrieval; it never turns Last Activity Date into activity evidence.

For portfolio views, scope Detailed Notes and Customer Task activity-history reads to the accounts already eligible for that routed view. Do not scan unrelated accounts.

## Cross-Workflow Consistency

For the same Airtable snapshot and account, Customer Task Centre — Renewal Focus and Account Follow-Up Builder must return the same persisted Last Verified Activity Date, Days Since Activity, source selection, and no-evidence result.

Account Follow-Up may differ only by showing a separately labeled newer current-conversation overlay.

## Final Rule

Last Verified Activity means the latest defensible dated account event, not the latest time Airtable changed. Prefer structured dated evidence, expand long text lazily, expose uncertainty instead of guessing, and keep the persisted calculation identical across every workflow that applies this contract.