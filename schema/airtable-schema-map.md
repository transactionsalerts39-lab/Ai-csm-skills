# Airtable Schema Map

Base: Book of Business Management  
Base ID: `app6O8peF5ywLe1GM`

## Tables

- Accounts table: `tblr6UnvfaqfNvwyU`
- Detailed Notes table: `tblI5cCnIY63S6pZq`
- Customer Tasks table: `tblWUzwjGM4pwhAqR`

## Key Account Fields

- Account Name: `fldOSLvopNOX6ae3Z`
- Engagement Status: `fldyrxDGOzWF3c7wm`
- Outreach Step: `fldhX3nTqX4a2eKt8`
- Activity notes: `flddz3lqUmEhLhmN5`
- Stage: `fldTYypHjPaFcatCi`
- ACV: `fldjieKzPumeF6afD`
- Renewal Date: `fldPmw5pHDNDgZYgA`
- Current Active CSM: `fldTQWeUcqj5HQoAH`
- Last Activity Date: `fld2jD1HJm9RRwNBW`
- Churn Risk: `fldy4GIC8xDuPjS8y`
- Meeting Sync established: `fld8kFpch7M4wGfpQ`
- Cadence Frequency: `flddcR78KacwfLyk5`
- Task status: `fldaYegYsT0eA3NAK`
- Customer Tasks linked records: `fldaetB5w1BersFeV`

## Detailed Notes Fields

- Notes: `fldcfsEsMgFHoB8VH`
- Title: `fldbf738tn5U18z7D`
- Account: `fldkJTerhbTfcObzR`
- Activity Type: `fldfi0aH7CncdNtb9`
- Date: `fld5Dd0gO8vHadVjl`
- Next Steps: `flduD4nsj6ZkEXmLZ`
- Last activity date: `fldYG5croDSgYQmNN`
- Customer Tasks linked records: `fld5rGBShRUYrYIiR`

## Customer Tasks Fields

Customer Tasks is the execution tracker. Use it for durable next steps, customer-waiting items, and check-off logic. Activity notes remain the historical narrative; Detailed Notes remain the meeting/activity detail store.

- Task Title: `fldbPKh3KNG9vbeXS`
- Account: `fldMIaXj2PtqHkmNk`
- Owner: `fldVF6TQNNaPmv8qf`
- Status: `fldFbZYJvZUZmrGf2`
- Priority: `fldGixN6MmFNAbTdG`
- Due Date: `fldm4uWSm23HVxZPC`
- Customer Waiting?: `fldDXgxychawzNCZn`
- Source Type: `fldRaStx1IoK4ooqv`
- Source Date: `fldp9gpKEdSIywADe`
- Source Summary: `fldKEtfu2JfA5VTKK`
- Source Detail Note: `fldo4zvmVhOXqed2p`
- Related Email / Meeting Summary: `fldhj5zbRG2dwT2sF`
- Completion Evidence: `fldsWVTfAAagVBTPT`
- Completed Date: `fldcOYlYd9dfmyl5K`
- Needs Review: `fld5SXTsSrQNl96t2`
- Last Updated From: `fldg0rM7UT6Jt148a`

## Customer Tasks Values

### Owner

Use only these exact values:

- `Ranjodh`
- `Customer`
- `AE / AM`
- `Support`
- `Internal Team`
- `Shared`
- `Unknown`

### Status

Use only these exact values:

- `Open`
- `In Progress`
- `Waiting on Internal Team`
- `Waiting on Customer`
- `Blocked`
- `Done`
- `Cancelled`
- `Needs Review`

Closed statuses:

- `Done`
- `Cancelled`

Open/actionable statuses:

- `Open`
- `In Progress`
- `Waiting on Internal Team`
- `Waiting on Customer`
- `Blocked`
- `Needs Review`

### Priority

Use only these exact values:

- `P1 - Must do`
- `P2 - Should do`
- `P3 - Monitor`

### Source Type

Use only these exact values:

- `Email`
- `Meeting`
- `Transcript`
- `Slack / Internal`
- `Support`
- `Manual`
- `Other`

### Last Updated From

Use only these exact values:

- `Update Notes`
- `Meeting Summarizer`
- `Weekly Command Centre`
- `Task Centre`
- `Meeting Prep`
- `Manual`

## Account-Level Task Status Summary

The Accounts table `Task status` field is a summary signal only. Customer Tasks is the source of truth.

Use this account-level mapping when task rows change:

- Set `Open` when one or more linked Customer Tasks are actionable: `Open`, `In Progress`, `Waiting on Internal Team`, `Waiting on Customer`, `Blocked`, or `Needs Review`.
- Set `yet to start` when one or more linked Customer Tasks exist but all actionable work has not started and the exact field value is available.
- Set `closed` when no linked Customer Tasks remain actionable and the exact field value is available.
- If a value is unavailable in Airtable, do not force-create a new select option unless the user explicitly asks.

## Cadence Frequency Values

- Weekly
- Bi-weekly
- Monthly
- Quarterly
- Ad hoc / as needed
- Paused
- TBD / not confirmed

## Record Identity

Use Airtable record IDs internally for exact task updates. Do not expose or require Ranjodh to remember record IDs. Natural-language task actions must resolve to the internal record ID before writing.

## Shared Contracts

- Task state and matching: `contracts/task-lifecycle.md`
- Write boundaries: `contracts/write-safety.md`
- Fiscal dates: `contracts/fiscal-calendar.md`
- Portfolio assignment scope: `contracts/portfolio-scope.md`

## Implementation Rule

When Airtable structure changes, update this schema map first, then affected shared contracts, then affected skill files, and finally the registry only if routing changed.