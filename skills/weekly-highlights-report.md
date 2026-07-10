---
name: weekly-highlights-report-generator-v5
description: >
  Generate a senior-director-ready weekly Highlights and Lowlights report from Airtable account
  activity. Use this for /weekly highlights, /weekly highlights and lowlights, /weekly report,
  /highlights report, /lowlights report, /weekly account report, or similar requests.
---

## Shared Contracts

Apply `contracts/fiscal-calendar.md`, `contracts/portfolio-scope.md`, `contracts/write-safety.md`, and `contracts/untrusted-input.md`. Use `schema/airtable-schema-map.md` for Airtable IDs. If this skill conflicts with a shared contract, the contract wins.

---

# Weekly Highlights and Lowlights Report Generator - V5

## Purpose

Generate a concise weekly account report for senior-leadership review. The report must separate accounts into:

1. **Highlights** - accounts with meaningful positive movement, progress, customer engagement, renewal/commercial movement, adoption improvement, or recovery momentum during the target week.
2. **Lowlights** - accounts with meaningful risk, stagnation, churn/downsell movement, customer silence, blocker escalation, negative sentiment, or renewal slippage during the target week.

This is not an activity dump. The report should only include accounts where the weekly movement is important enough for a senior director to read.

---

## V5 Senior Director Standard

Use the WoW Upper Commercial PDF style as the calibration point:

- Weekly date header.
- Separate **Highlights** and **Lowlights** sections.
- Each entry is one bullet.
- Each entry includes account name, ACV, renewal quarter, the movement, and owner.
- Entries are short but meaningful.
- The report includes only accounts with clear movement, not every account touched that week.

Good entries usually show one of these:

- Renewal moved forward, renewal confirmed, OF / DocuSign / legal / signature motion progressed.
- Account recovered from churn risk, ghosting, silence, or negative sentiment.
- Customer re-engaged after a long gap.
- Executive or leadership alignment improved.
- New use case, expansion, upsell, predictive/SI/AI Email/data-pack interest, or maturity model path emerged.
- A real blocker was resolved, escalated, or became material to renewal.
- Customer gave churn notice, confirmed churn, went silent, no-showed, pushed back legally, cited budget/value issues, or had a major stakeholder/business change.

Do not include routine work unless it materially changes the account story.

---

## Trigger Examples

Use this skill when the user says:

- `/weekly highlights`
- `/weekly highlights and lowlights`
- `/weekly report`
- `/highlights report`
- `/lowlights report`
- `/weekly account report`
- `/pull report for this week`
- `/weekly update from Airtable`
- `weekly highlights`
- `what should I submit for WoW`

---

## Airtable Configuration

Base ID: `app6O8peF5ywLe1GM` (Book of Business Management)
Accounts table ID: `tblr6UnvfaqfNvwyU`
Detailed Notes table ID: `tblI5cCnIY63S6pZq`
Timezone: Asia/Kolkata

### Accounts Table - Key Fields

| Field | ID | Type | Purpose |
|---|---|---|---|
| Account Name | `fldOSLvopNOX6ae3Z` | singleLineText | Account name for the report |
| Activity notes | `flddz3lqUmEhLhmN5` | multilineText | Free-text running log with inline dates - parse carefully |
| ACV | `fldjieKzPumeF6afD` | currency | Annual contract value - round to nearest k |
| Renewal Date | `fldPmw5pHDNDgZYgA` | date | Used to derive renewal quarter and sort order |
| Last Activity Date | `fld2jD1HJm9RRwNBW` | lastModifiedTime | Auto-field - use only to identify recently touched accounts |
| Stage | `fldTYypHjPaFcatCi` | multipleSelects | Current account stage / motion signal |
| Churn Risk | `fldy4GIC8xDuPjS8y` | singleSelect | Risk status |

### Customer Tasks Evidence

Use Customer Tasks as supporting evidence when Source Date or Completed Date falls inside the target week, or when an active task materially explains the account's movement. A task status change alone is not automatically senior-worthy.

### Detailed Notes Table - Key Fields

| Field | ID | Type | Purpose |
|---|---|---|---|
| Notes | `fldcfsEsMgFHoB8VH` | multilineText | Detailed activity notes |
| Title | `fldbf738tn5U18z7D` | singleLineText | Activity title |
| Account | `fldkJTerhbTfcObzR` | multipleRecordLinks | Linked account name |
| Activity Type | `fldfi0aH7CncdNtb9` | singleSelect | Meeting Notes, Account Update, Overview activity notes, Support ticket, etc. |
| Date | `fld5Dd0gO8vHadVjl` | date | Manually set date - primary filter for this table |
| Next Steps | `flduD4nsj6ZkEXmLZ` | multilineText | Documented next steps |

---

## Default Scope

If the user does not specify a date range:

- Use the current work week, Monday through Friday, in Asia/Kolkata.
- Include only Accounts where Current Active CSM = `Ranjodh` unless the user explicitly overrides the portfolio scope.
- Calculate and include the ISO week number.
- Pull any account with meaningful movement during that target week.
- Do not restrict to renewal accounts only.
- Do not restrict to current-quarter accounts only.

If the user specifies a date range such as `last week`, `March 16-20`, or `this week`, use that range.

---

## Workflow

### Step 1 - Determine Target Week

- Default to the current work week, Monday through Friday.
- If a different range is specified, use that range.
- Calculate the ISO week number for the header.

Header format:

```text
Week [ISO_WEEK_NUMBER] | [DAY_OF_WEEK], [FULL_DATE] | Monday [START_DATE] - Friday [END_DATE]
```

---

### Step 2 - Pull Data from Detailed Notes Table

This is the clean structured source. Each row is already a discrete activity entry with its own Date field.

Use Airtable `list_records_for_table` to fetch records where `Date` (`fld5Dd0gO8vHadVjl`) falls within the target week.

Fields to pull:

- Notes
- Title
- Account
- Activity Type
- Date
- Next Steps

Use the Detailed Notes Date field as the primary date source. Do not use Last Modified Date for this table.

---

### Step 3 - Pull Data from Accounts Table

The Accounts table is the free-text running source. `Activity notes` (`flddz3lqUmEhLhmN5`) can contain multiple weeks inside one field.

Use Airtable `list_records_for_table` to fetch records where `Last Activity Date` (`fld2jD1HJm9RRwNBW`) falls within the target week. This only identifies accounts recently touched; it does not prove customer movement.

Fields to pull:

- Account Name
- Activity notes
- ACV
- Renewal Date
- Stage
- Churn Risk

If the Detailed Notes table identifies an account whose Accounts record was not fetched by Last Activity Date, fetch that Accounts record too so ACV, Renewal Date, Stage, and Churn Risk can be used.

---

### Step 4 - Parse Inline Dates from Account Activity Notes

The `Activity notes` field may contain historical entries. Extract only entries dated within the target Monday-Friday range.

Common date formats:

- `9 march : They sent me an email...`
- `10 march 26 : They have declined...`
- `11 march : We have a meeting again...`
- `25 march : Met with Emily Sansom...`
- `25march : Customer confirmed...`
- `3/25 : Customer replied...`

Parsing rules:

- Split the text by lines.
- Look for DD month, DD month YY, DD month YYYY, DDmon, DD mon, M/D, or M/D/YY patterns.
- Match month names case-insensitively.
- If no year is specified, assume the current year in Asia/Kolkata.
- Include only entries whose parsed date falls within the target week.
- If a line has no date marker, treat it as a continuation of the prior dated entry.
- Skip unclear dates rather than guessing.

---

### Step 5 - Merge Weekly Evidence by Account

An account may appear in both tables. Merge all relevant weekly evidence before classifying.

For each account, combine:

- Detailed Notes Title, Notes, Date, Activity Type, and Next Steps
- Accounts Activity notes entries for the target week
- Account metadata: ACV, Renewal Date, Stage, Churn Risk

Deduplicate overlapping updates. Prefer substantive customer or commercial movement over internal/admin notes.

---

## Step 6 - Apply the Senior-Worthy Filter

Only include an account if it has a clear weekly movement that matters to leadership.

### Include as a Highlight only if one or more is true

- Renewal moved closer to close, signature, OF, DocuSign, legal review, or commit.
- Confirmed renewal, early renewal, closed-won, or meaningful save/retention path.
- Churn-risk account showed real recovery momentum.
- Customer re-engaged after silence or ghosting.
- Executive/leadership stakeholder engaged or reset meeting happened.
- Customer accepted a plan, roadmap, maturity model path, ROI plan, or implementation path.
- Adoption improved in a way that can support renewal/value proof.
- Expansion or upsell signal surfaced and is specific enough to pursue.
- Technical/product blocker was resolved or workaround confirmed in a way that improves account confidence.

### Include as a Lowlight only if one or more is true

- Confirmed churn, churn notice, non-renewal signal, early termination request, or $0 forecast movement.
- Customer went silent after meaningful prior engagement, especially near renewal.
- No-show, repeated reschedule, or inability to secure a meeting has materially slowed the account.
- Renewal/commercial path slipped, legal escalated, payment/collections issue surfaced, or budget was lost.
- Customer raised value, ROI, data quality, product, integration, or support concerns that affect renewal confidence.
- Stakeholder loss, acquisition, restructuring, or leadership change creates renewal uncertainty.
- A material support or workflow blocker remains unresolved.
- Customer sentiment turned negative or a competitive threat surfaced.

### Exclude unless there is a clear reason

Do not include:

- Generic follow-up sent.
- Routine cadence held with no decision or movement.
- Internal field cleanup or Airtable/Salesforce hygiene.
- Meeting scheduled with no meaningful account implication.
- Stable healthy accounts with no meaningful progress.
- Low-value admin updates.
- Every account that had activity.

Exception: A generic follow-up can qualify as a Lowlight only when it proves stalled momentum, weak engagement, or renewal risk.

---

## Step 7 - Determine Account Metadata

For each included account, use Accounts table metadata:

### ACV

- Round to the nearest thousand.
- Format as `XXk`.
- If ACV is missing, use `ACV unknown` instead of inventing.

Examples:

- `$87,490.67` -> `87k`
- `$115,400` -> `115k`

### Renewal Quarter

Convert Renewal Date to quarter format.

Examples:

- `2026-05-31` -> `Q2 26`
- `2027-01-27` -> `Q1 27`

Quarter mapping:

- Use `contracts/fiscal-calendar.md`.
- January belongs to Q4 of the prior fiscal year.
- Compute the label dynamically; do not use calendar quarters.

If Renewal Date is missing, use `Quarter unknown` rather than guessing.

---

## Step 8 - Sort Like the WoW Submission

Entries can come from any account, but sort them by renewal urgency and quarter so the report reads like the senior-director examples.

Sort order:

1. Current renewal quarter first.
2. Next renewal quarter.
3. Later quarters in chronological order.
4. Future-year renewals after current-year renewals.
5. Unknown renewal quarter last.

Within the same quarter, sort by leadership importance:

1. Confirmed churn / closed won / commit / legal / signature / commercial movement.
2. High-risk current-quarter renewal movement.
3. Executive or leadership alignment.
4. Adoption/value/ROI movement.
5. Other meaningful but lower-urgency movement.

Do not sort only by activity volume. Activity volume is not the signal.

---

## Step 9 - Write the Narrative

Each bullet must be one concise senior-readable sentence.

The narrative should capture:

- What changed this week.
- Why it matters.
- The renewal/commercial/adoption/risk implication.
- The next step only if it is important to the account story.

Tone:

- Direct.
- Manager/senior-director ready.
- Plain language.
- No filler.
- No transcript-style detail.
- No hidden/internal strategy beyond what is appropriate for manager review.

Preferred sentence pattern:

```text
* [Account Name] - [ACV] - [QX YY] - [meaningful movement and why it matters]. - Ranjodh
```

Good examples:

```text
* Ottimate - 87k - Q2 26 - Audience workflow was published and tested, creating the first concrete ROI path ahead of the May renewal. - Ranjodh
* Appomni - 91k - Q4 26 - Completed the first maturity model discussion and aligned the customer on the assessment as the next step. - Ranjodh
* NetWitness - 115k - Q3 26 - Another outreach went unanswered, so the account remains stalled with no clear stakeholder or renewal path. - Ranjodh
* onPhase (DocuPhase) - 90k - Q3 26 - Customer remains on a churn path after formal notice, with feedback meeting still not fully locked. - Ranjodh
```

Bad examples:

```text
* Sent follow-up email. - Ranjodh
* Had a meeting with the customer. - Ranjodh
* Updated Airtable notes and next steps. - Ranjodh
* Account is stable. - Ranjodh
```

---

## Step 10 - Report Size and Quality Bar

Default output should be tight.

Guidance:

- Target 3-6 Highlights and 3-6 Lowlights maximum.
- Fewer is better if only a few accounts truly moved.
- It is acceptable to output `None this week` for Highlights or Lowlights.
- Do not pad the report to make it look full.
- If more than six items truly qualify in one section, include them only if each is senior-worthy.

Before finalizing, ask for each bullet:

- Would my Senior Director care about this?
- Did something change this week?
- Is there a clear renewal, commercial, adoption, value, risk, or executive-stakeholder implication?
- Is this stronger than a routine activity note?

If the answer is no, omit it.

---

## Step 11 - Required Output Format

Use this format exactly.

```text
Week [ISO_WEEK_NUMBER] | [DAY_OF_WEEK], [FULL_DATE] | Monday [START_DATE] - Friday [END_DATE]

Highlights:
* [Account Name] - [ACV] - [QX YY] - [Summary narrative]. - Ranjodh
* [Account Name] - [ACV] - [QX YY] - [Summary narrative]. - Ranjodh

Lowlights:
* [Account Name] - [ACV] - [QX YY] - [Summary narrative]. - Ranjodh
* [Account Name] - [ACV] - [QX YY] - [Summary narrative]. - Ranjodh
```

Formatting rules:

- Use `*` bullets.
- Use ` - ` separators.
- Always append ` - Ranjodh` at the end of each bullet unless the user explicitly asks for team-wide owner attribution.
- Do not use pipe separators.
- Do not add labels like `Status`, `Why`, `Signal`, or `Next Step` inside each line.
- Keep each bullet to one sentence where possible.
- Do not include an evidence table by default.
- Do not include raw Airtable notes.

If there are no qualifying items:

```text
Highlights:
None this week.

Lowlights:
None this week.
```

---

## Step 12 - Output Delivery

Default delivery:

- Output inline in the chat as copy-paste-ready text.
- Do not create a file, artifact, HTML, or visualizer output unless the user explicitly asks for a downloadable export.

If `message_compose_v1` is available, use:

- `kind`: `other`
- `summary_title`: `Weekly Account Highlights and Lowlights - Week [#], [Month Day-Day], [Year]`
- single variant labeled `Weekly Highlights and Lowlights`

If `message_compose_v1` is not available, output directly in the conversation.

---

## Edge Cases

### No activity found for the week

Say:

```text
No account activity was detected for the target week. Please provide another date range if you want me to check a different week.
```

### Activity exists but no senior-worthy movement

Say:

```text
Weekly activity exists, but none of it meets the senior-director threshold for a meaningful highlight or lowlight.
```

### Only Highlights found

Output the Highlights section and:

```text
Lowlights:
None this week.
```

### Only Lowlights found

Output:

```text
Highlights:
None this week.
```

Then include the Lowlights section.

### Ambiguous dates in free-text notes

Skip unclear entries rather than guessing. If skipping materially affects the report, add one short note after the report.

### Mixed signals

Classify based on the dominant movement that week. If the account has both strong progress and material risk, choose the stronger leadership signal and mention the tension in one sentence.

Example:

```text
* Gainsystems - 92k - Q3 26 - Renewal remains risky, but customer value from campaigns and workflow plans moved the account in a more positive direction this week. - Ranjodh
```

### Lowlight recovery becomes Highlight

If an account was previously a Lowlight but this week shows real recovery, include it as a Highlight and state the change clearly.

Example:

```text
* Price Spider - 82k - Q2 26 - Re-engaged after months of silence and asked for guidance on expanding 6sense usage. - Ranjodh
```

### Highlight degrades into Lowlight

If an account was previously positive but this week deteriorates, include it as a Lowlight.

Example:

```text
* Cognosos - 89k - Q2 26 - PoC went silent after previously exploring a workaround, putting the renewal path back at risk. - Ranjodh
```

---

## Final Rule

This skill should produce a short, judgment-based weekly submission that reads like the WoW Upper Commercial examples: account, ACV, quarter, meaningful movement, and owner. It should not be a full activity recap. When in doubt, omit low-signal items and preserve only the highlights/lowlights that a senior director should actually see.
