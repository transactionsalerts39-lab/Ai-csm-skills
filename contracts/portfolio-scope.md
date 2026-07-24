# Portfolio Scope and Evidence Contract

Apply this contract to Weekly Command Centre, CSM Sentiment Notes, Weekly Highlights, Manager Weekly Recap, Weekly Slack Update, Lattice Round-Up, and portfolio-level Clari workflows.

## Default Assignment Scope

Include only Accounts where Current Active CSM = `Ranjodh`.

Override this only when the user explicitly requests all accounts, a different CSM, or a named account outside the default portfolio. State the override in the output.

## Cadence Eligibility and Denominator

For portfolio cadence reporting, the eligible set is the complete assignment scope: every Accounts record where Current Active CSM = `Ranjodh`. The assignment field alone determines eligibility.

- Do not remove an assigned account from the denominator because Engagement Status, Outreach Step, legacy Stage, Churn Risk, renewal state, cadence fields, notes, or tasks describe it as inactive, parked, paused, ad hoc, churned, or offboarding.
- If an assigned account should no longer require cadence, keep it as a non-active cadence exception until Current Active CSM is changed or cleared in Airtable. Do not infer or perform reassignment during a read/report.
- For an explicit assignee override, use every account in that requested assignment scope as the eligible denominator.
- Cadence coverage percentage = verified fully active cadence accounts / all cadence-eligible accounts. Never use only accounts already marked `Cadence active` or only accounts with populated cadence fields as the denominator.

## Evidence Priority

Use:

1. Structured Detailed Notes dated inside the requested period
2. Customer Tasks with relevant Source Date, Completed Date, or active status
3. Dated entries inside Accounts → Activity notes
4. Account metadata for renewal, risk, ACV, status, and cadence context

Last Activity Date is a retrieval signal, not proof of customer movement.

Do not count metadata edits, rollups, automation touches, or generic CRM cleanup as customer movement.

## CSM Sentiment Portfolio Completeness

For a portfolio CSM Sentiment Notes run, include every account in the assignment scope exactly once.

Reconcile:

`Eligible Accounts = Ready Drafts + Needs CSM Review + Insufficient Evidence`

Do not omit inactive, parked, churned, offboarding, low-engagement, or stale accounts. If an account lacks enough evidence for a credible note, show it under `Insufficient Evidence` rather than inventing a health assessment.

## Reporting Boundary

Portfolio reports are read-only by default. Show recommendations without writing them unless the user explicitly asks to apply changes.
