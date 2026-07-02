---
name: lattice-round-up
description: >
  Create a manager-facing weekly operating recap that summarizes portfolio movement,
  account risks, renewal focus, competitor/vendor signals, hygiene updates, and next-week
  priorities. Use this when the user asks for /lattice round up, /lattice roundup,
  /manager weekly recap, /weekly manager update, /manager update, /weekly portfolio recap,
  /what should I send my manager, or Emily update.
---

# Lattice Round-Up

## Purpose

Create a concise manager-facing weekly operating recap for Ranjodh's book of business.

This is not the same as `/weekly highlights`.

- `/weekly highlights` = senior-director WoW submission with only meaningful Highlights and Lowlights.
- `/lattice round up` = manager-facing recap with broader portfolio context, hygiene updates, competitor/vendor signals, account movement, risks, blockers, and next-week focus.

The output should help Ranjodh's manager quickly understand:

- What was completed across the portfolio
- Which accounts moved meaningfully
- Which accounts are risky, stalled, or need escalation
- Which current-quarter and next-quarter renewals need attention
- What competitor/vendor signals were captured
- What Ranjodh is focused on next week

This should not become a raw activity dump.

---

## Trigger Examples

Use this skill when the user says:

- `/lattice round up`
- `/lattice roundup`
- `/lattice weekly recap`
- `/manager weekly recap`
- `/manager recap`
- `/weekly recap for manager`
- `/manager update`
- `/weekly manager update`
- `/weekly portfolio recap`
- `/what should I send my manager`
- `Emily update`

If the user says `/manager weekly recap`, use this workflow unless they specifically ask for another format.

---

## Airtable Source of Truth

Use Airtable as the primary source when the user asks to pull the recap from account data.

Base:
- **Book of Business Management**
- Base ID: `app6O8peF5ywLe1GM`

Primary table:
- **Accounts**
- Table ID: `tblr6UnvfaqfNvwyU`

Secondary table:
- **Activity/Notes**
- Table ID: `tblI5cCnIY63S6pZq`

### Accounts Table - Key Fields

| Field | Field ID | Type | How to Use |
|---|---|---|---|
| Account Name | `fldOSLvopNOX6ae3Z` | singleLineText | Account name |
| Activity notes | `flddz3lqUmEhLhmN5` | multilineText | Primary running account context |
| Stage | `fldTYypHjPaFcatCi` | multipleSelects | Account motion / task / risk signal |
| ACV | `fldjieKzPumeF6afD` | currency | Account value and priority |
| Renewal Date | `fldPmw5pHDNDgZYgA` | date | Renewal timing and sorting |
| Last Activity Date | `fld2jD1HJm9RRwNBW` | lastModifiedTime | Recent record-touch signal only |
| Churn Risk | `fldy4GIC8xDuPjS8y` | singleSelect | Risk signal; field may appear as Chrun riks |
| Task status | `fldaYegYsT0eA3NAK` | singleSelect | Open / closed task signal |

### Activity/Notes Table - Supporting Fields

| Field | Field ID | Type | How to Use |
|---|---|---|---|
| Notes | `fldcfsEsMgFHoB8VH` | multilineText | Detailed activity note |
| Title | `fldbf738tn5U18z7D` | singleLineText | Activity title |
| Account | `fldkJTerhbTfcObzR` | multipleRecordLinks | Linked account |
| Activity Type | `fldfi0aH7CncdNtb9` | singleSelect | Meeting Notes, Account Update, Support ticket, etc. |
| Date | `fld5Dd0gO8vHadVjl` | date | Primary date for structured activity |
| Next Steps | `flduD4nsj6ZkEXmLZ` | multilineText | Strong source for future actions |

---

## Default Scope

If the user does not specify a date range:

- Use the current Monday-Friday work week in Asia/Kolkata.
- Prioritize accounts renewing in the current quarter first.
- Then include next-quarter renewals.
- Then include later-quarter accounts only if there was meaningful manager-worthy movement.

If the user provides a pasted weekly update instead of asking to pull from Airtable:

- Use the pasted update as the primary source.
- Do not invent Airtable data.
- Structure, compress, and prioritize the content using this skill.

---

## What Counts as Manager-Worthy

Include account updates when they show one or more of the following:

- Current-quarter or next-quarter renewal movement
- Contract review, order form, DocuSign, legal, signature, or commercial progress
- Customer re-engagement after silence
- Executive or leadership alignment
- Meaningful cadence improvement
- New or changed stakeholder ownership
- Clear product adoption, workflow, reporting, or use-case progress
- Support blocker that could affect confidence or renewal
- Competitive/vendor signal that may affect account strategy
- Payment, invoice, procurement, or collection issue
- Customer silence, no-shows, stalled outbound-only motion, or no stakeholder alignment
- Churn risk, downsell risk, budget/value concern, or renewal uncertainty

Exclude low-signal activity such as:

- Generic follow-up sent with no account implication
- Routine sync held with no meaningful update
- Airtable cleanup by itself unless it supports forecast or renewal hygiene
- Stable account with no movement
- Meeting scheduled with no meaningful context
- Internal-only admin work that does not matter to the manager

---

## Workflow

### Step 1 - Identify Inputs

Determine whether the user has provided:

1. A pasted weekly manager update
2. A request to pull from Airtable
3. A mix of pasted notes and Airtable context

If pasted notes are provided, use them directly.

If pulling from Airtable, gather:

- Current-week activity
- Current-quarter renewals
- Next-quarter renewals
- Stalled or risky accounts
- Competitor/vendor signals if present in notes
- Next steps and blockers

---

### Step 2 - Pull Portfolio Hygiene Updates

Capture top-level operating work such as:

- Total logged VO/MBO
- Total logged EBR next dates
- Sentiment score updates
- Forecast updates
- FY26/FY27 deal hygiene
- Last Steps and Next Steps updates
- Renewal risk field cleanup
- ACV / renewal forecast category updates
- Any other portfolio-level cleanup that matters to the manager

Do not over-explain these. Keep them short.

---

### Step 3 - Pull Competitor / Vendor Signals

Extract competitor or vendor tags from the notes.

Examples:

- Clay
- HockeyStack
- ZoomInfo
- Demandbase
- Qualified
- UserGems
- Influ2
- HubSpot
- Salesforce
- Marketo

Include these only when they indicate:

- Competitive pressure
- Tech-stack change
- Possible churn/downsell reason
- Strategic account context
- Vendor displacement or replacement risk
- Customer dependency that affects workflow or adoption

Output them in a table.

---

### Step 4 - Pull Manager-Worthy Account Movement

For each account, identify:

- Account name
- Renewal timing / quarter if available
- What changed this week
- Why it matters to the manager
- Whether it is positive movement, mixed movement, or risk

Sort by:

1. Current-quarter renewal accounts
2. Next-quarter renewal accounts
3. Future-quarter accounts with meaningful movement
4. Unknown renewal timing last

Within the same renewal period, sort by leadership importance:

1. Commercial / contract / renewal movement
2. High-risk or stalled current-quarter accounts
3. Executive alignment or stakeholder transition
4. Support blockers affecting renewal/adoption
5. Adoption / workflow / cadence progress

---

### Step 5 - Pull Risks / Stalled Accounts

Include accounts where the manager should know about risk or lack of traction.

Common risk signals:

- Past-due invoice
- Fourth or later follow-up pending
- No customer response
- Outbound-only activity
- No confirmed stakeholder alignment
- No clear renewal path
- Support blocker still unresolved
- Customer asked to terminate / churn / pause
- Customer is waiting on internal update
- Stakeholder left or backfill not onboarded yet
- Renewal is close but no meeting is locked

For each risk account, provide:

- Account
- Risk signal
- Next move

---

### Step 6 - Create Focus for Next Week

Create 3-5 bullets max.

Focus items should be practical and tied to account movement or risk reduction.

Examples:

- Move Ottimate Marketo-first workflow dry run forward and capture the result.
- Lock Sandler working session and clarify Trilliad transition priorities.
- Push Brightspot and NetWitness from outbound-only status into confirmed next meetings or escalation paths.
- Re-engage Truework after the new stakeholder starts and schedule the intro / overview call.

Do not include vague items like `continue monitoring` unless there is no better action.

---

## Required Output Format

Use this exact structure by default.

```text
Lattice Round-Up
Week [ISO_WEEK_NUMBER] | [START_DATE] - [END_DATE]

Highlight for the week:
- [Portfolio hygiene / operating update]
- [Portfolio hygiene / operating update]
- [Meaningful weekly achievement]

Competitor / Vendor Signals:
| Account | Vendor / Competitor | Signal |
|---|---|---|
| [Account] | [Vendor] | [Why it matters] |

Manager-Worthy Account Movement:
| Priority | Account | Renewal Timing | Movement | Manager Read |
|---|---|---|---|---|
| 1 | [Account] | [QX YY / Current quarter / Next quarter / Unknown] | [What changed] | [Why manager should care] |

Risks / Stalled Accounts:
| Account | Risk Signal | Next Move |
|---|---|---|
| [Account] | [Risk] | [Action] |

Focus for Next Week:
- [Top priority]
- [Top priority]
- [Top priority]
```

---

## Formatting Rules

- Keep the recap manager-ready and concise.
- Use tables for competitor signals, account movement, and risks.
- Use bullets for weekly highlights and next-week focus.
- Do not create a raw activity dump.
- Do not include every account touched.
- Do not include stable accounts unless there is meaningful movement.
- Keep each account row short enough to scan quickly.
- Use plain business language.
- Do not make the tone overly formal.
- Do not create a downloadable file unless the user explicitly asks.

---

## Example Output

```text
Lattice Round-Up
Week 21 | May 18 - May 22

Highlight for the week:
- Total logged VO/MBO: 11.
- Updated sentiment score across all accounts.
- Updated Last Steps and Next Steps for FY26 deals.

Competitor / Vendor Signals:
| Account | Vendor / Competitor | Signal |
|---|---|---|
| onPhase (DocuPhase) | Clay | Competitive/vendor signal captured. |
| Beyond Identity | Clay | Competitive/vendor signal captured. |
| Honeycomb.io | HockeyStack | Competitive/vendor signal captured. |
| PerformanceCentre Inc | ZoomInfo | Competitive/vendor signal captured. |

Manager-Worthy Account Movement:
| Priority | Account | Renewal Timing | Movement | Manager Read |
|---|---|---|---|---|
| 1 | Ottimate | Current-quarter priority | Support clarified SI Dashboard segment filter logic, and Marketo-first workflow dry run is being prepared. | Meaningful technical progress on a renewal-sensitive account. |
| 2 | Appomni | Future-quarter / cadence build | Monthly sync secured. | Positive cadence improvement and stronger account control. |
| 3 | Checker | Contract review | Meeting landed for contract review. | Commercial motion worth tracking. |
| 4 | ATG USA | Future-quarter | Current setup and active use cases clarified; broader EBR moved to May 20. | Better account discovery and cleaner EBR path. |
| 5 | Sandler | Next-quarter focus | Follow-up sent to lock working session around setup, usage gaps, and Trilliad transition planning. | Needs working session to convert scattered engagement into a plan. |

Risks / Stalled Accounts:
| Account | Risk Signal | Next Move |
|---|---|---|
| Brightspot | Past-due invoice INV61901 for $6,354.74 and 4th follow-up pending. | Continue escalation/follow-up and confirm payment path. |
| NetWitness | Movement is still outbound-only with no customer response or stakeholder alignment. | Re-engage with a stronger stakeholder/renewal value ask. |
| Truework | Stakeholder backfill starts May 18; current motion depends on onboarding the new owner. | Re-engage after May 18 to schedule intro / overview with Adam and the new stakeholder. |

Focus for Next Week:
- Move Ottimate Marketo-first workflow dry run forward and capture the outcome.
- Lock Sandler working session and clarify Trilliad transition priorities.
- Push Brightspot / NetWitness from outbound-only status into confirmed next meetings or escalation paths.
```

---

## Edge Cases

### No competitor/vendor signals

If there are no competitor or vendor signals, write:

```text
Competitor / Vendor Signals:
None captured this week.
```

### No meaningful risks

If there are no risks or stalled accounts worth surfacing, write:

```text
Risks / Stalled Accounts:
No manager-worthy stalled or risk accounts surfaced this week.
```

### Activity exists but is too low-signal

If the user provides a long activity dump but only a few items matter, include only the meaningful items and omit the rest.

### Missing renewal timing

If renewal date or quarter is missing, use:

```text
Unknown
```

Do not guess renewal timing.

### Manager asks for shorter version

If the user asks for a short version, output only:

```text
Highlight for the week:
- [3 bullets max]

Accounts needing attention:
- [Account] - [reason/action]
- [Account] - [reason/action]

Focus for next week:
- [3 bullets max]
```

---

## Final Rule

The Lattice Round-Up should feel like a clean weekly operating update to a manager: sharp, prioritized, renewal-aware, and focused on movement, risk, and next actions. It should not read like Airtable notes, a task list, or a senior-director WoW highlights/lowlights submission.
