# V10 Account Activity Recency Regression Cases

Run these as dry-run scenarios. Do not modify Airtable, Notion, Gmail, Calendar, Slack, or another customer system.

Canonical contract: `contracts/account-activity-recency.md`.

Assume today is evaluated in `Asia/Kolkata`.

| # | Scenario | Expected result |
|---:|---|---|
| 1 | Detailed Note dated Aug 8 documents a customer meeting; Accounts Last Activity Date is Aug 11 | Last Verified Activity = Aug 8 · Detailed Note |
| 2 | Detailed Note Aug 8 and qualifying completed Customer Task Aug 10 | Last Verified Activity = Aug 10 · Customer Task completion |
| 3 | Qualifying task Source Date Aug 11 from Email and completed task Aug 10 | Last Verified Activity = Aug 11 · Customer Task source |
| 4 | Dated Activity-notes event Aug 12 and latest structured candidate Aug 11 | Last Verified Activity = Aug 12 · dated Activity note, provided the Activity note is actually inspected through the retrieval gate |
| 5 | Detailed Note and task completion both qualify on Aug 10 | Detailed Note wins displayed source tie-break |
| 6 | Task completion and task source both qualify on Aug 10 | Customer Task completion wins displayed source tie-break |
| 7 | Task Due Date Aug 12 with no completed/source event on that date | Due Date is ignored for activity |
| 8 | Airtable task record was created today for future work | Record creation is ignored for activity |
| 9 | Last Clari Sync Date is today | Clari sync is ignored for activity |
| 10 | Accounts Last Activity Date is today because a cadence field changed | Metadata date is ignored for activity |
| 11 | Customer Task Source Type = Meeting, Source Date = Aug 10, source context confirms meeting occurred | Aug 10 qualifies |
| 12 | Customer Task Source Type = Email, Source Date = Aug 10, source context confirms customer email | Aug 10 qualifies |
| 13 | Customer Task Source Type = Support, Source Date = Aug 10, context says support case was raised | Aug 10 qualifies |
| 14 | Customer Task Source Type = Manual, Source Date = Aug 10, context only says `follow up next week` | Does not qualify |
| 15 | Customer Task Source Type = Manual, Source Date = Aug 10, context explicitly says requested report was sent to customer | May qualify as explicit completed customer-facing activity |
| 16 | Done task has Completed Date Aug 10 but no meaningful Completion Evidence | Completed Date alone does not qualify |
| 17 | Done task has Completed Date Aug 10 and evidence says report was delivered to customer | Aug 10 qualifies |
| 18 | Detailed Note has Date Aug 10 but is only an internal planning placeholder | Does not qualify |
| 19 | Activity notes contain an undated customer update | Does not qualify because date cannot be established safely |
| 20 | Candidate activity date is tomorrow | Exclude from calculation, flag Needs review, and never return negative Days Since Activity |
| 21 | Latest qualifying date is today | Days Since Activity = 0 |
| 22 | Latest qualifying date is 4 days ago | Days Since Activity = 4 |
| 23 | No qualifying evidence exists | `No verified dated activity found`; Days Since Activity = N/A; source = None verified |
| 24 | Structured candidate is Aug 10 and Accounts Last Activity Date is Aug 9 | Full Activity notes are not fetched merely for recency |
| 25 | Structured candidate is Aug 10 and Accounts Last Activity Date is Aug 12 | Activity notes may be fetched and inspected for an explicitly dated event newer than Aug 10 |
| 26 | Renewal Focus and Account Follow-Up use the same Airtable snapshot | Persisted date, Days Since Activity, source tie-break, and no-evidence result match exactly |
| 27 | Account Follow-Up current turn proves a completed customer event today; Airtable persisted result is Aug 10 | Persisted result remains Aug 10; separate newer unlogged activity = today |
| 28 | Account Follow-Up current turn contains a draft or future promise | No newer unlogged activity |
| 29 | Renewal Focus runs | Activity calculation uses Airtable only; zero Notion and Gmail calls |
| 30 | Account Follow-Up runs without explicit Gmail request | Activity calculation uses permitted Airtable/current-conversation evidence; zero Gmail calls |
| 31 | Renewal Focus has Detailed Notes for activity recency | Cadence Field Status still uses structured Meeting Sync + Cadence Frequency only |
| 32 | Activity calculation succeeds but active-task retrieval is incomplete | Activity value may be shown, but task completeness is separately labeled incomplete; do not conflate the two checks |

## Cross-Workflow Output Contract

Renewal Focus main rows include:

- `Last Verified Activity` formatted as `[date] · [source]`, or the no-evidence label
- `Days Since Activity`

Account Follow-Up review workspaces begin with:

```text
Account Activity Check
- Last verified Airtable activity: [date] — [N days ago / today / no-evidence label]
- Source: [canonical source]
- Activity: [short factual summary]
- Newer unlogged activity: [current-turn event or None]
```

Then show `All Active Associated Tasks`.

## Pass Criteria

- Last Verified Activity is the latest defensible dated substantive account event, not a record-modification timestamp.
- Future dates, due dates, task creation, Clari sync, drafts, plans, and metadata edits never qualify by themselves.
- Same-date source selection follows Detailed Notes → Customer Task completion → Customer Task source → dated Activity note.
- Activity notes are expanded lazily through the Last Activity Date retrieval gate; the metadata value never becomes evidence.
- Both workflows return the same persisted result from the same Airtable snapshot.
- Account Follow-Up may only add a separately labeled current-conversation overlay.
- Renewal Focus remains read-only and Airtable-only.
- Account Follow-Up remains Read / Draft and does not access Gmail by default.