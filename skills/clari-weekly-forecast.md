---
name: clari-weekly-forecast-prep
description: >
  Prepare weekly Clari forecasting updates from a Clari CSV export, Airtable account activity,
  and optional Salesforce opportunity context. Use this when the user asks what to fill in
  Clari, wants weekly forecast updates, uploads a Clari opportunity export, or asks for
  renewal forecast prep by quarter. This version compares against the uploaded Clari CSV,
  always shows Last Steps (the latest existing value or a new incremental append) and the complete current/final Next Steps for every included opportunity, preserves deduplication, enforces paste-ready date/RS formatting for Clari text fields, and drafts brief current-quarter and next-quarter churn forecast comments.
---

## Shared Contracts

Apply `contracts/fiscal-calendar.md`, `contracts/portfolio-scope.md`, `contracts/write-safety.md`, and `contracts/untrusted-input.md`. Use `contracts/task-lifecycle.md` and `schema/airtable-schema-map.md` for Customer Tasks and Airtable IDs. If this skill conflicts with a shared contract, the contract wins.

---

# Clari Weekly Forecast Prep — Guaranteed Last Steps + Final Next Steps

## Purpose

Generate review-first, paste-ready weekly Clari updates for the fields Ranjodh maintains:

- Last Steps visibility for every included opportunity: incremental append when new, otherwise latest existing value
- Complete current/final Next Steps for every included opportunity
- Renewal Risk Notes
- ACV Amount / Expected Renewal ACV / Churn/Downsell Risk Amount recommendations
- Brief current-quarter and next-quarter churn forecast comments

The skill should combine:

- The uploaded Clari CSV export as the current state of record
- Airtable account activity and notes
- Optional Salesforce opportunity context
- Meeting notes, customer emails, and raw updates already stored in Airtable

The main goal is to make both Last Steps and Next Steps visible on every included opportunity without duplicating Clari updates. Compare proposed updates against the existing CSV values before recommending anything to paste. When no change is needed, repeat the relevant current Clari value and label it unchanged. It must also produce concise churn comments for the current quarter and next quarter when the user is updating renewal forecast rollups.

Do not directly update Clari unless a confirmed Clari or Salesforce writeback connector is available and the user explicitly asks to update.

---

## Trigger Examples

Use this skill when the user says things like:

- `/weekly clari forecast`
- `/clari forecast prep`
- `/weekly clari update`
- `/forecast prep`
- `/what do I fill in Clari`
- `/Clari next steps`
- `/renewal forecast update`
- `/update forecast fields`
- `/ACV change review`
- `/forecast Q2`
- `/Q2 Clari update`
- `/prepare my quarter forecast`
- `/update Clari for this quarter`
- `give me incremental last steps`
- `compare against this Clari CSV`
- `do not duplicate current Clari steps`

---

## Critical Behavior Changes

### 1. Uploaded CSV Is the Current Clari Baseline

When the user uploads a Clari CSV, treat it as the current state of Clari fields.

Use it to read what is already in:

- `Last Steps`
- `Next Steps`
- `Renewal Risk Notes`
- `Stage`
- `Renewal Forecast Category`
- `Close Date`
- `Base Line ACV`
- `ACV Amount`
- `Expected Renewal ACV`
- `Churn/Downsell Risk Amount`
- `Churn/Downsell Risk Category`
- `Churn/Downsell Risk Reason`
- `Customer Healthscore Status`
- `Engagement Health`
- `Opportunity ID`

If a Clari CSV is not uploaded, ask for it before generating incremental Last Steps, unless the user explicitly says to draft from Airtable only.

### 2. Always Show Last Steps

For every included opportunity, populate `Last Steps — Latest / Append`.

- If a new completed action is supported and not already represented in the CSV, show the paste-ready incremental append and label `Last Steps: Append` in `Update Type`.
- If no new completed action exists and the CSV Last Steps are populated, repeat the latest existing Clari Last Step verbatim and label `Last Steps: Unchanged`.
- If neither the CSV nor approved evidence supports a completed action, show `Evidence gap — no supported Last Steps value.`, label `Last Steps: Evidence gap`, and add the opportunity to **Needs Manual Review**.

Do not output a full Last Steps replacement by default. Never present an already-represented action as a new append.

### 3. Always Show the Complete Current/Final Next Steps

For every included opportunity, populate `Next Steps — Final Value` after comparing against the CSV.

- If the current CSV Next Steps remain accurate, repeat the complete current value verbatim and label `Next Steps: Unchanged`.
- If the current value is stale, unclear, incomplete, too long, or contains completed work, provide the complete paste-ready final replacement and label `Next Steps: Replace`.
- If the CSV field is blank and a forward-looking action is supported, provide the complete paste-ready value and label `Next Steps: New`.
- If neither the CSV nor approved evidence supports a forward-looking action, show `Evidence gap — no supported Next Steps value.`, label `Next Steps: Evidence gap`, and add the opportunity to **Needs Manual Review**.

Never use `No Next Steps change recommended.` as a substitute for the actual current/final value.

### 4. Separate Completed Actions From Future Actions

Never place the same idea in both Last Steps and Next Steps.

- Last Steps = completed actions only
- Next Steps = forward-looking actions only

If an action was previously listed as a Next Step and Airtable now confirms it happened, include it as an incremental Last Step and recommend a cleaned Next Steps value that removes the completed item.


### 5. Paste-Ready Date and Initials Formatting Is Mandatory

All Clari text-field recommendations must be directly copy-paste-ready. Do not output review labels like `New:`, `Replace:`, `Add risk note:`, or `No change unless...` inside a field value unless the user specifically asks for review commentary instead of paste-ready values.

Use Ranjodh's initials `RS` in every proposed Clari text-field value:

- **New Last Steps append:** `M/D RS - [completed action].`
- **New or replacement Next Steps:** `M/D RS - [forward-looking action]; [second action]; [third action].`
- **Renewal Risk Notes:** `Month DD, YYYY RS - [risk summary]. [What needs to happen next / what would reduce risk].`

When a Clari table includes `Last Steps`, `Next Steps`, or `Renewal Risk Notes`, the cell must contain the actual value, not an instruction to replace, add, review, or look elsewhere. Newly proposed values must use the required date/RS format. An unchanged existing Clari value may be repeated verbatim and identified as unchanged in `Update Type`.

Forbidden table-cell outputs include `Replace—see block below`, `Replace—see below`, `See paste-ready block`, `Add risk note below`, or any equivalent cross-reference. The table is the canonical copy-paste surface.

Use review language only in `Update Type`, `ACV / Churn-Downsell Amount`, `Confidence`, or `Needs Manual Review`, not inside supported paste-ready text values. The two exact evidence-gap markers are allowed only when no supported field value exists.

---

## Step 1 — Confirm Forecast Quarter Scope

Before pulling or generating account updates, ask the user which forecast quarter they are updating, unless the user already provided the quarter or date range.

Ask:

```text
Are you updating Clari for this quarter, Q2, or a different quarter? I can filter the export to Q2 only, or you can say Q3, Q4, all quarters, or provide a custom date range.
```

## Fiscal Quarter Mapping

Use Ranjodh’s fiscal quarter definition, not standard calendar quarters.

- Q1 = February 1 – April 30
- Q2 = May 1 – July 31
- Q3 = August 1 – October 31
- Q4 = November 1 – January 31

For 2026:

- Q2 2026 = May 1, 2026 – July 31, 2026
- Q3 2026 = August 1, 2026 – October 31, 2026
- Q4 2026 = November 1, 2026 – January 31, 2027

If the current date falls inside a fiscal quarter and the user says “this quarter,” use the current fiscal quarter.

Example:

```text
This quarter = Q2 2026 = May 1, 2026 – July 31, 2026.
```

---

## Step 2 — Read the Clari CSV

Read the uploaded Clari CSV first.

### Required / Expected CSV Columns

Read these fields when available:

- `Opportunity`
- `Account Name`
- `Owner`
- `Sales Owner`
- `Stage`
- `Renewal Forecast Category`
- `Contract Expiration Close Date`
- `Close Date`
- `Base Line ACV`
- `ACV Amount`
- `Expected Renewal ACV`
- `Incremental ACV`
- `Churn/Downsell Risk Amount`
- `Churn/Downsell Risk Category`
- `Churn/Downsell Risk Reason`
- `Renewal Risk Notes`
- `Best Case Expected Renewal ACV`
- `Best Case Churn/Downsell Risk Amount`
- `Renew Term (# of Months)`
- `Last Steps`
- `Next Steps`
- `Manager Notes`
- `Metrics`
- `Economic Buyer`
- `Decision Process`
- `Decision Criteria`
- `Paper Process`
- `Implicate Pain`
- `Champion`
- `Competition`
- `Customer Healthscore Status`
- `Engagement Health`
- `Account CSM`
- `Auto-Renew`
- `Business Outcomes`
- `Primary Loss Reason`
- `Opportunity ID`
- `Opportunity Currency`

If the export contains duplicate column names, such as duplicate `Last Steps` columns, use the populated column and flag the duplicate under **Needs Manual Review**.

Unless the user explicitly requests all owners, filter the CSV to Account CSM = `Ranjodh` when that column is available. If the column is unavailable, match to Airtable and apply Current Active CSM = `Ranjodh`.

---

## Step 3 — Apply Quarter / Date Filter

After quarter scope is confirmed, filter the CSV before enriching with Airtable.

### Filtering Priority

Use this order:

1. Clari `Close Date`
2. Airtable `Renewal Date`, only if Close Date is blank or needs validation
3. User-provided custom date range, if applicable

### Include Row If

- `Close Date` falls within the selected fiscal quarter, or
- `Close Date` is blank but Airtable `Renewal Date` falls within the selected fiscal quarter, or
- User specifically asks to include all quarters.

### Flag Row If

- Clari `Close Date` and Airtable `Renewal Date` are in different quarters
- Close Date is missing
- Close Date format is ambiguous
- Opportunity appears to be in selected quarter but account renewal metadata says otherwise

---

## Step 4 — Match CSV Rows to Airtable Accounts

For each filtered CSV row, match to Airtable using this order:

1. `Opportunity ID` if a Salesforce/Clari opportunity connector is available
2. Exact `Account Name`
3. Strong fuzzy match on `Account Name`
4. User-provided account name

Use Airtable as the working source for recent activity, but use the Clari CSV as the current state of Clari field values.

### Airtable Base

- Base name: Book of Business Management
- Base ID: `app6O8peF5ywLe1GM`

### Accounts Table

- Table ID: `tblr6UnvfaqfNvwyU`

Pull:

- Account Name (`fldOSLvopNOX6ae3Z`)
- Activity notes (`flddz3lqUmEhLhmN5`)
- ACV (`fldjieKzPumeF6afD`)
- Renewal Date (`fldPmw5pHDNDgZYgA`)
- Stage (`fldTYypHjPaFcatCi`)
- Churn Risk (`fldy4GIC8xDuPjS8y`)
- Last Activity Date (`fld2jD1HJm9RRwNBW`)

### Customer Tasks Table

Use active tasks and tasks completed since the CSV snapshot to support Next Steps and incremental Last Steps. Do not copy task titles blindly; translate only evidence-backed actions and completion into Clari-ready wording.

### Detailed Notes Table

- Table ID: `tblI5cCnIY63S6pZq`

Pull recent relevant records:

- Title (`fldbf738tn5U18z7D`)
- Notes (`fldcfsEsMgFHoB8VH`)
- Activity Type (`fldfi0aH7CncdNtb9`)
- Date (`fld5Dd0gO8vHadVjl`)
- Next Steps (`flduD4nsj6ZkEXmLZ`)

---

## Step 5 — Deduplication Logic

### Normalize Before Comparing

Before comparing existing CSV values with proposed updates, normalize the text by:

- Lowercasing
- Removing date prefixes like `5/7 RS -`, `May 07, 2026 :`, `4/29 IG -`
- Removing owner initials like `RS`, `IG` where they are only prefixes
- Removing extra punctuation
- Collapsing whitespace
- Comparing meaning, not exact wording only

### Treat These as Duplicates

Treat a proposed step as duplicate if it describes the same action as an existing CSV step, even with different wording.

Examples:

Existing CSV Last Step:

```text
4/28 RS - Zack reconnected to review 6sense usage/value and asked for guidance on creating net-new SF accounts.
```

Do not add:

```text
Zack reached out to review current usage and Salesforce account creation guidance.
```

Existing CSV Next Step:

```text
5/7 RS - Follow up with Heather on maturity model completion.
```

Do not add:

```text
Check with Heather on whether the maturity model survey is complete.
```

### Last Steps Selection

A proposed Last Steps append is valid only if all are true:

- It is completed.
- It is supported by Airtable, Salesforce, meeting notes, email notes, or user context.
- It is not already represented in CSV `Last Steps`.
- It is not a future action.

After testing for a valid append, still populate the row: show the append when valid, otherwise repeat the latest existing CSV Last Step, otherwise use the exact Last Steps evidence-gap marker and flag manual review.

### Next Steps Selection

A proposed new or replacement Next Steps value is valid only if all are true:

- Every included action is forward-looking.
- Every included action is specific and actionable.
- Completed work has been removed.
- The complete final value is supported by the approved sources.

After comparison, always populate the row with one of: the complete current value unchanged, a complete final new/replacement value, or the exact Next Steps evidence-gap marker plus manual review. If a cleaner replacement is better than retaining or appending text, provide the complete replacement and explain the reason in `Update Type` or surrounding commentary.

---

## Step 6 — Field Generation Rules

## A. Last Steps — Latest Existing or Incremental Append

Generate only completed actions as new append content.

Preferred format for a new append:

```text
M/D RS - [completed action].
```

Rules:

- Use the latest completed actions since the CSV Last Steps appear to have been updated.
- Keep to 1–4 incremental entries per account unless the user asks for all.
- Sort multiple incremental entries in reverse chronological order: newest date first, then older dates below it.
- Use the actual evidence date for sorting, not discovery order or source order.
- When two entries share the same date, use the more precise timestamp when available; otherwise preserve their source order.
- When placing the new block into an existing Clari Last Steps field, put it above older history so the full field remains newest-first.
- Include dates when available.
- Do not include open tasks.
- Do not repeat existing CSV Last Steps as new content.
- If there is no new completed action, repeat the latest existing CSV Last Step verbatim and label it unchanged.
- If no existing or evidence-backed completed action exists, use `Evidence gap — no supported Last Steps value.` and add the row to **Needs Manual Review**.

## B. Next Steps — Complete Current/Final Value

Generate future actions only.

Mandatory format for a new or replacement value:

```text
M/D RS - [next action]; [next action]; [next action].
```

Rules:

- Compare against the CSV `Next Steps` first.
- If current CSV Next Steps remain accurate, repeat the complete value verbatim and label it unchanged.
- If the current value is stale, incomplete, or contains completed work, provide the complete paste-ready replacement using `M/D RS -` at the start.
- If the current value is blank and a supported action exists, provide the complete new paste-ready value.
- Do not output only an incremental fragment; the cell must show the complete final Next Steps value.
- Keep new/replacement values to 1–3 actions.
- Prioritize renewal movement, customer engagement, stakeholder alignment, support blockers, ROI/value proof, commercial next steps, and Salesforce stage progression.
- Do not output `New:`, `Replace:`, `Add:`, or `Review:` inside the Next Steps value. Put that classification in `Update Type`.
- If no current or evidence-backed forward action exists, use `Evidence gap — no supported Next Steps value.` and add the row to **Needs Manual Review**.

## C. Renewal Risk Notes

Generate a new dated risk note only when there is new or changed risk context.

Mandatory paste-ready format:

```text
Month DD, YYYY RS - [risk summary]. [What needs to happen next / what would reduce risk].
```

Rules:

- Compare against existing CSV `Renewal Risk Notes`.
- Do not rewrite old risk notes unless the user asks.
- Add a new note only if there is materially new risk evidence, status change, or mitigation path.
- Do not mark confirmed churn unless the evidence clearly supports it.
- Include `RS` after the written date in every proposed Renewal Risk Notes value.
- Do not output `Add risk note`, `No change`, or other review labels inside the Renewal Risk Notes value. Use those labels only outside the paste-ready field value.

## D. Churn Commit / Churn Forecast Comments

Generate brief rollup comments for the Clari **Churn Commit (Call)** / churn forecast comment box when requested or when the user is working on forecast rollups.

These comments are separate from account-level `Renewal Risk Notes`. They are short manager-facing forecast notes that summarize why the churn amount exists for the selected quarter.

### Required Churn Comment Scope

By default, generate two comments:

1. **Current Quarter Churn Comment**
2. **Next Quarter Churn Comment**

Use Ranjodh’s fiscal quarter mapping:

- Q1 = February 1 – April 30
- Q2 = May 1 – July 31
- Q3 = August 1 – October 31
- Q4 = November 1 – January 31

If today is in Q2, then:

- Current quarter = Q2
- Next quarter = Q3

### Which Accounts to Include

For each quarter, include only accounts that materially contribute to churn/downsell forecast or churn risk. Prioritize:

- Accounts with `Renewal Forecast Category` = Churn, Churn Commit, Churn Best Case, or similar
- Accounts with non-zero `Churn/Downsell Risk Amount`
- Accounts marked Confirmed churn or high-risk in Airtable
- Accounts with recent account notes showing non-renewal, low engagement, payment/collections risk, stakeholder loss, unresolved blocker, commercial dispute, or value concern

Do not include healthy or stable accounts just because they renew in the quarter.

### Churn Comment Style

Keep each quarter comment short enough to paste into Clari. Use one account per line or semicolon-separated clauses.

Preferred format:

```text
[Account] : [one-line churn/risk reason]
[Account] : [one-line churn/risk reason]
[Account] : [one-line churn/risk reason]
```

or, when space is tight:

```text
[Account] : [risk reason]; [Account] : [risk reason]; [Account] : [risk reason]
```

### Churn Comment Rules

- Keep each account reason to one short phrase or sentence.
- Mention only the strongest churn driver.
- Use customer/account language from Airtable or CSV where possible.
- Do not invent churn drivers.
- Do not over-explain.
- Do not include full renewal risk notes here.
- If there is no churn risk in a quarter, output: `No material churn comment needed for this quarter.`
- If the comment already exists in a screenshot/pasted note and remains accurate, say `No churn comment change recommended.`
- If new account context changes the comment, provide a replacement comment.

### Example

```text
Ottimate : Renewal risk elevated due to auto-renewal/terms concerns, unproven ROI, and high platform cost.
Brightspot : Low engagement plus past-due invoice signal.
Sandler : Mixed risk; Sarah engagement is positive, but stakeholder transition and contract/features walkthrough are still pending.
```

## E. ACV / Forecast Amount Recommendations

Do not guess ACV.

Recommend ACV changes only when evidence supports a commercial position.

Evaluate:

- Base Line ACV
- ACV Amount
- Expected Renewal ACV
- Churn/Downsell Risk Amount
- Swing ACV / Incremental ACV
- Renewal Forecast Category
- Close Date
- Renewal Risk Notes
- Salesforce stage
- Customer commitment or churn confirmation

Rules:

- If Confirmed Churn and evidence supports no renewal, suggest Expected Renewal ACV = $0 and Churn/Downsell Risk Amount = Base Line ACV.
- If Churn Risk exists but renewal is still recoverable, do not automatically set ACV to $0.
- If downsell amount is known, recommend Expected Renewal ACV = Base Line ACV minus confirmed downsell.
- If no commercial evidence exists, output `No ACV change recommended.`
- Always include rationale and confidence.

---

## Step 7 — Default Output Format

Always start with a scope summary:

```md
Clari Forecast Prep
Quarter Scope: [QX YYYY / All Quarters / Custom Date Range]
Date Range: [date range]
CSV Rows Reviewed: [number]
Rows Included After Filter: [number]
Rows Excluded Outside Scope: [number]
Rows with Last Steps cells: [number]
Rows with Next Steps cells: [number]
Field Coverage: [Pass/Fail] — Rows Included = Rows with Last Steps cells = Rows with Next Steps cells
Evidence Gaps: [Last Steps count] Last Steps · [Next Steps count] Next Steps
```

Then output one complete Clari Update Table. This table is the default and canonical account-level copy-paste surface.

If working on forecast rollups, output Churn Commit Comments after the table. Add Needs Manual Review only when necessary.

## Clari Update Table

| Account | Quarter / Close | Forecast / Stage | Last Steps — Latest / Append | Next Steps — Final Value | Update Type | Renewal Risk Notes — Append | ACV / Churn-Downsell Amount | Confidence |
|---|---|---|---|---|---|---|---|---|
| [Account] | [QX · Close Date] | [Forecast Category · Stage] | `M/D RS - [completed action].`, latest existing Last Step, or the exact evidence-gap marker | `M/D RS - [complete forward-looking actions].`, complete current CSV value, or the exact evidence-gap marker | Last Steps: Append/Unchanged/Evidence gap; Next Steps: New/Replace/Unchanged/Evidence gap | `Month DD, YYYY RS - [risk summary]. [Mitigation/required next move].` or `No renewal risk note change.` | [Exact recommendation or `No ACV change recommended.`] | [High/Medium/Low] |

### Table Rules

- Populate both Last Steps and Next Steps cells for every included opportunity.
- Put the actual current/final or paste-ready value directly in the relevant table cell.
- Never output `Replace—see block below`, `see below`, `see paste-ready block`, or any other placeholder/cross-reference.
- New Last Steps append content must use `M/D RS - [completed action].`, contain completed actions only, and list multiple entries newest-first. For example, `7/11` must appear above `7/8`.
- When no Last Steps append is needed, repeat the latest existing Clari Last Step verbatim and label it unchanged.
- Next Steps must show the complete current/final field value. When new or replacement content is required, use `M/D RS - [forward-looking actions].` and provide the full value—not an instruction or incremental fragment.
- Put `Append`, `New`, `Replace`, `Unchanged`, or `Evidence gap` only in `Update Type`, not inside a supported field value.
- If a supported value does not exist, use the applicable exact evidence-gap marker and add the opportunity to **Needs Manual Review**.
- Renewal Risk Notes must use `Month DD, YYYY RS - [risk summary]. [Mitigation].` and remain separate from amount recommendations.
- `ACV / Churn-Downsell Amount` is only for supported numeric/forecast-field recommendations and rationale; do not place Renewal Risk Notes there.
- Use `No renewal risk note change.` or `No ACV change recommended.` when applicable.
- Keep cells concise enough to scan, but do not shorten them into instructions that require another section.

## Churn Commit Comments

When churn comments are required, include this section after the Clari Update Table and before account paste-ready blocks.

Format:

```md
## Churn Commit Comments

### Current Quarter — [QX YYYY]
```text
[brief Clari-ready churn comment]
```

### Next Quarter — [QX YYYY]
```text
[brief Clari-ready churn comment]
```
```

Rules:

- These are rollup comments, not account-level Renewal Risk Notes.
- Keep them brief: one or two lines per account at most.
- Include only accounts driving churn or material churn risk in that quarter.
- If no comment is needed, say so.

---

## Step 8 — Optional Paste-Ready Account Blocks

Do not output separate account blocks by default. The Clari Update Table already contains the canonical paste-ready values.

Provide account blocks only when the user explicitly asks for:

- account blocks
- one account at a time
- separate copy boxes
- expanded paste-ready format

When requested, copy the exact same field values from the table without changing wording, dates, initials, or recommendations. Never use an account block as a destination for content omitted from the table.

## Step 9 — Needs Manual Review

Create a **Needs Manual Review** section for rows where:

- The CSV and Airtable account match is uncertain
- ACV change is uncertain
- Salesforce and Airtable conflict
- Clari field is blank but no supportable value exists
- Either guaranteed field uses an evidence-gap marker
- Duplicate columns were detected
- Churn/downsell amount requires manager/commercial confirmation
- Close Date and Airtable Renewal Date fall in different quarters
- Close Date is blank or ambiguous
- Existing CSV Last Steps or Next Steps contain outdated owner initials that should be corrected
- Current Clari `Next Steps` appear to include completed work that should be moved to `Last Steps`

Format:

| Account | Opportunity | Issue | Recommendation |
|---|---|---|---|
| [Account] | [Opportunity] | [Issue] | [Recommendation] |

---

## Step 10 — Output Modes

### Default Mode

Output:

1. Scope summary
2. Complete Clari Update Table containing all account-level paste-ready values
3. Churn Commit Comments when required
4. Needs Manual Review when required

Do not output separate paste-ready account blocks in Default Mode.

### Table Only

If the user asks for table only, output only the Clari Update Table.

### Optional Account Blocks

Use only when the user explicitly asks for account blocks, separate copy boxes, or one-account-at-a-time output. Repeat the exact table values; do not move missing content out of the table.

### Incremental Last Steps Only

If the user asks for only incremental Last Steps, output only:

| Account | Opportunity | Last Steps — Latest / Append | Update Type | Confidence |
|---|---|---|---|---|

### Next Steps Only

If the user asks for only Next Steps, output only:

| Account | Opportunity | Current CSV Next Steps | Next Steps — Final Value | Update Type | Confidence |
|---|---|---|---|---|---|

Every `Next Steps — Final Value` cell must contain the complete current value, the complete new/replacement `M/D RS - ...` value, or the exact Next Steps evidence-gap marker plus manual review.

### Full Detail Mode

Use only when the user asks for full detail. Keep the complete paste-ready table first, then add source evidence and comparison commentary. Do not replace table values with cross-references.

## Guardrails

## Do Not Guess

Do not invent:

- Renewal status
- Churn confirmation
- ACV changes
- Executive buyer alignment
- Customer commitment
- Completed Last Steps
- Support resolution
- Salesforce stage readiness

## Do Not Duplicate

Do not repeat content already present in the uploaded CSV Last Steps or Next Steps.

This is the most important rule in this skill.

When there is no new information, repeat the relevant current Clari value and label it unchanged. If the current value is blank and no approved evidence supports one, use the exact evidence-gap marker and flag manual review rather than inventing activity. Safe internal review activity may be used only when it actually happened or is explicitly requested by the user, and any proposed change must follow the required date/RS format.

## CSV Comparison Is Required

If a CSV is uploaded, always compare against it before producing updates.

If the CSV is missing, say:

```text
Please upload the current Clari CSV so I can avoid duplicating existing Last Steps and Next Steps.
```

## Source-of-Truth Rule

Use this priority order:

1. Salesforce, if available
2. Clari CSV for current Clari field values
3. Airtable for recent activity and account context
4. User-provided meeting notes / emails / raw context

If sources conflict, do not silently choose one. Flag the conflict and explain what should be verified.

## ACV Safety Rule

Text fields can be generated when evidence exists.

ACV fields must be treated carefully.

For:

- ACV Amount
- Expected Renewal ACV
- Churn/Downsell Risk Amount
- Swing ACV / Incremental ACV

Use this rule:

```text
Suggest only. Do not update unless there is clear commercial evidence or user approval.
```

---

## Suggested Weekly Operating Model

Before forecast review:

```text
/weekly clari forecast
Use this Clari export and Airtable context. I’m updating Q2. Always show Last Steps and the complete current/final Next Steps for every included opportunity; deduplicate only the proposed changes.
```

After customer meetings or emails:

```text
/update notes
[raw customer update, email, or meeting note]
```

Then rerun:

```text
/clari forecast prep
Refresh Q2 based on the latest Airtable notes and compare against the uploaded CSV.
```

---

## Final Rule

This skill should make weekly Clari updating fast, safe, and non-duplicative.

The output should clearly show:

- What is new since the uploaded CSV
- The new Last Steps append or, when unchanged, the latest existing Last Step
- The complete current/final Next Steps value and whether it is new, replaced, or unchanged
- Whether Renewal Risk Notes need a new dated update
- What brief churn comment to paste for current quarter and next quarter
- Whether any ACV / forecast field needs review

Default to one complete Clari Update Table, followed only by Churn Commit Comments and Needs Manual Review when applicable. Separate account blocks are opt-in. Every included row must contain both a Last Steps cell and a Next Steps cell. Reconcile `Rows Included = Rows with Last Steps cells = Rows with Next Steps cells`; regenerate missing cells or flag the evidence gap before returning the result. All proposed Clari text-field values must appear directly in the table and use the required date and `RS` initials format.
