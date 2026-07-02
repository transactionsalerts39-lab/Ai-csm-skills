---
name: weekly-slack-update
description: >
  Generate Slack-ready weekly account updates from Airtable for customer/account Slack channels.
  Use this when the user asks for weekly Slack updates, customer channel updates, account Slack
  notes, renewal Slack updates, or asks to pull current-year renewal accounts from Airtable and
  summarize account movement for an internal Slack audience.
---

# Weekly Slack Update

## Purpose

Create concise, Slack-ready weekly account updates for internal customer/account channels.

The audience is usually:
- Ranjodh
- Manager
- AE / AM
- SC / Campaign Ops / Support stakeholders
- Other internal team members following the customer channel

The update should help the internal team quickly understand:
- What moved recently
- Current account momentum
- Positive signals
- Concerns or risks
- The next action Ranjodh should take

This is not a manager recap, not a raw Airtable dump, and not a full meeting summary.

---

## Trigger Examples

Use this skill when the user says:

- `/weekly slack update`
- `/slack weekly update`
- `/customer channel update`
- `/weekly slack notes`
- `/slack account update`
- `pull slack updates for renewals`
- `give me weekly Slack updates for accounts renewing this year`
- `write customer channel updates`
- `use Weekly Slack Update`
- `weekly slack update for [Account Name]`

---

## Airtable Source of Truth

Use Airtable as the primary source.

### Base

- **Base name:** Book of Business Management
- **Base ID:** `app6O8peF5ywLe1GM`

### Accounts Table

- **Table:** Accounts
- **Table ID:** `tblr6UnvfaqfNvwyU`

Use these fields:

| Field | Field ID | Use |
|---|---|---|
| Account Name | `fldOSLvopNOX6ae3Z` | Account name |
| Activity notes | `flddz3lqUmEhLhmN5` | Primary source for running account updates |
| Stage | `fldTYypHjPaFcatCi` | Working status / action signal |
| ACV | `fldjieKzPumeF6afD` | Useful for prioritization; do not include unless helpful |
| Renewal Date | `fldPmw5pHDNDgZYgA` | Used to filter current-year renewals and identify urgency |
| Last Activity Date | `fld2jD1HJm9RRwNBW` | Supporting freshness signal, not the same as customer activity |
| Churn Risk | `fldy4GIC8xDuPjS8y` | Risk status; field may appear as “Chrun riks” in Airtable |

### Activity/Notes Table

- **Table:** Activity/Notes
- **Table ID:** `tblI5cCnIY63S6pZq`

Use these fields as supporting context:

| Field | Field ID | Use |
|---|---|---|
| Notes | `fldcfsEsMgFHoB8VH` | Detailed notes |
| Title | `fldbf738tn5U18z7D` | Activity title |
| Account | `fldkJTerhbTfcObzR` | Linked account |
| Activity Type | `fldfi0aH7CncdNtb9` | Meeting Notes, Account Update, Support ticket, etc. |
| Date | `fld5Dd0gO8vHadVjl` | Activity date |
| Next Steps | `flduD4nsj6ZkEXmLZ` | Strong source for next action |

---

## Default Scope

If the user does not specify accounts or a date range:

1. Pull all Accounts where **Renewal Date** falls within the current calendar year.
2. Generate one Slack update per account.
3. Use the current work week for the header:
   - Monday through Friday
   - Include the ISO week number
4. Sort accounts by renewal date ascending, then by risk level.

If the user says:
- “this year” → use current calendar year unless they specify fiscal year.
- “Q2” / “Q3” / “Q4” → use the requested quarter if dates are clear.
- “this week” → use current Monday–Friday.
- “last week” → use previous Monday–Friday.
- specific account names → only generate updates for those accounts.

---

## Workflow

### Step 1 — Pull Renewal Accounts

From the Accounts table, pull accounts where Renewal Date falls in the requested range.

Default:
- Current calendar year
- Example: if today is in 2026, pull Renewal Date from 2026-01-01 through 2026-12-31.

Fields to retrieve:
- Account Name
- Activity notes
- Stage
- ACV
- Renewal Date
- Last Activity Date
- Churn Risk

### Step 2 — Pull Supporting Activity

For each account, search the Activity/Notes table for matching linked or mentioned activity.

Retrieve:
- Notes
- Title
- Activity Type
- Date
- Next Steps

Use only the most relevant recent entries. Prioritize:
- Current week
- Previous week
- Most recent customer-facing update
- Renewal-risk updates
- Support blockers
- Commercial/AE updates
- Meeting notes
- Explicit next steps

### Step 3 — Find “Last Movement”

“Last movement” should be the most recent meaningful account event, not just the Airtable Last Activity Date.

Good examples:
- Customer replied with availability
- Meeting happened
- Support ticket was escalated or resolved
- Renewal conversation moved forward
- Customer raised a blocker
- Customer went unresponsive after outreach
- Invoice/collections issue surfaced
- Technical blocker or workflow issue was identified

Bad examples:
- Airtable record was edited
- Generic internal cleanup
- Minor metadata update
- Unclear note with no account movement

Format:

```text
Last movement: [Date] update — [short summary]
```

If no meaningful movement is found:

```text
Last movement: No meaningful recent customer-facing movement found in Airtable
```

### Step 4 — Write the Current Read

Write 2–3 concise sentences that answer:
- Is momentum strong, weak, stalled, or mixed?
- Is the customer engaged?
- Is the renewal at risk?
- Is there a blocker?
- Is there a clear value story?
- What should the internal team understand quickly?

Do not over-explain. This should sound natural in Slack.

### Step 5 — Write Positive Signal

Write one short sentence.

Examples:
- There is still time to re-engage before renewal.
- Customer is engaged and has a clear next meeting path.
- There is a concrete use case that can anchor the next conversation.
- Internal stakeholders are aligned on the next action.
- Support/Engineering is actively investigating the blocker.

If there is no positive signal:

```text
Positive signal:
No clear positive signal visible in the latest notes.
```

### Step 6 — Write Concern / Risk

Write one short sentence.

Examples:
- No confirmed owner, meeting, or renewal conversation is currently active.
- Renewal is close and engagement remains weak.
- The account has a support blocker that could impact confidence.
- Customer has stated value concerns that need executive/commercial alignment.
- Stakeholder transition creates risk if the new owner is not onboarded quickly.

If there is no material risk:

```text
Concern / risk:
No material risk visible in the latest notes.
```

### Step 7 — Write Next Step

Write one clear action.

The next step should be specific and operational:
- Send outreach
- Book meeting
- Follow up with AE
- Escalate support blocker
- Prepare ROI/value recap
- Confirm renewal intent
- Share contract/current usage overview
- Schedule working session
- Confirm customer owner

Avoid generic “continue monitoring” unless that is truly the right move.

---

## Required Output Format

Default output should be easy to copy into Slack.

Use one code block per account when possible.

```text
Weekly update: Week [#] | [Start Date]–[End Date]

[Account Name]

Last movement: [Date] update — [short summary]

Current read:
[2–3 sentence account read.]

Positive signal:
[1 sentence]

Concern / risk:
[1 sentence]

Next step:
[1 clear action]
```

---

## Multi-Account Output Rules

When generating updates for multiple accounts:

1. Sort by renewal date ascending.
2. Put each account in its own copyable code block.
3. Add a short table summary above the blocks only if there are more than 5 accounts.

Summary table format:

| Account | Renewal Date | Momentum | Risk | Next Step |
|---|---:|---|---|---|

Momentum values:
- Strong
- Mixed
- Weak
- Stalled
- Unknown

Risk values:
- High
- Medium
- Low
- Unknown

Keep the table short. The copyable Slack blocks are the main deliverable.

---

## Slack Style Rules

Tone should be:
- Direct
- Internal-friendly
- Clear
- Practical
- Not too formal
- Not too long

Write like a CSM giving a useful internal account update.

Do:
- Mention renewal timing when relevant.
- Mention blockers clearly.
- Mention stakeholder gaps clearly.
- Mention support/commercial issues when they matter.
- Keep next steps action-oriented.

Do not:
- Include raw Airtable notes.
- Include irrelevant historical detail.
- Overstate risk.
- Invent customer sentiment.
- Invent meetings or owners.
- Include customer-sensitive language that should not be broadly shared.
- Write long paragraphs.

---

## Handling Risk and Churn

Use Airtable Churn Risk as a signal, but verify against notes.

If Churn Risk = Confirmed churn:
- Treat as high risk unless notes clearly show the risk is stale or resolved.
- State the active save path if one exists.
- Do not say the account is recoverable unless Airtable notes support it.

If Churn Risk is blank:
- Do not invent risk.
- Use renewal date, engagement, blockers, and notes to describe momentum.

If renewal is within 90 days and engagement is weak:
- Call out concern clearly.
- Next step should be direct renewal/value outreach.

---

## Handling Missing Data

If the account has limited notes:

```text
Current read:
There is limited recent context in Airtable, so the account read is based mainly on renewal timing, stage, and the latest available activity. Momentum is unclear until we confirm the current stakeholder, usage story, and renewal path.
```

If no next step is obvious:

```text
Next step:
Confirm the current customer owner and schedule a renewal priorities / usage review.
```

---

## Example

```text
Weekly update: Week 20 | May 6–10

Brightspot

Last movement: Apr 8 update

Current read:
Brightspot has very little recent signal beyond an April follow-up asking if they want to connect. Momentum is weak, and the July renewal makes the lack of engagement a real concern. There is no clear stakeholder, product motion, or value story visible in the notes right now.

Positive signal:
There is still a window to re-engage before the renewal gets too close.

Concern / risk:
No confirmed owner, meeting, or renewal conversation is currently active.

Next step:
Prioritize direct outreach, identify the real owner internally, and use a “renewal priorities / usage review” ask instead of a generic check-in.
```

---

## Edge Cases

### User asks for one account
Output only one copyable Slack block.

### User asks for a table only
Output only the table summary.

### User asks for current-year renewals
Pull all renewal accounts in the current calendar year and output one block per account.

### User asks for “all risky accounts”
Filter by Churn Risk, renewal timing, weak engagement, or stage values like `fu 3`, `fu 4`, `Support needed`, or `Task pending`.

### Too many accounts
If more than 15 accounts are returned, group by renewal month or quarter and keep each account block concise.

---

## Final Rule

The output should be Slack-ready and copy-paste friendly.

It should help the internal team quickly understand what changed, what the account read is, what the risk is, and what Ranjodh is doing next.
