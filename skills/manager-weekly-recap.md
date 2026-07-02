---
name: manager-weekly-recap
description: Generate a curated, manager-facing weekly account recap from Airtable data. Use this skill whenever Ranjodh asks for a weekly recap, manager update, weekly summary for their manager, or any variation of "send my manager a weekly update." Also trigger when the user says things like "prepare my manager recap," "write up my week for my manager," or "what should I send my manager this week." This is distinct from the weekly highlights/lowlights skill — this skill produces a narrative, judgment-based summary optimized for a manager audience, not a team update grid. Always use this skill when the target audience is the manager.
---

# Manager Weekly Recap Skill

## Purpose

Generate a curated, judgment-based weekly account recap for Ranjodh's manager. This is NOT a raw activity dump. It is a confident, manager-readable summary that surfaces:

1. **Portfolio Pulse** — A 3–4 sentence overview of the week across the book of business
2. **Accounts Worth Manager Visibility** — Curated list (typically 3–6 accounts) that either progressed meaningfully or need manager awareness due to risk or stall
3. **Renewal Radar** — Accounts renewing within the next 60–90 days and their current momentum
4. **Portfolio Trend Signal** — 1–2 observations about patterns visible across accounts this week

The output is always delivered via `message_compose_v1` as inline copyable text. Never as a file, artifact, or HTML widget.

---

## Airtable Configuration

- **Base ID:** `app6O8peF5ywLe1GM` (Book of Business Management)
- **Accounts Table ID:** `tblr6UnvfaqfNvwyU`
- **Activity/Notes Table ID:** `tblI5cCnIY63S6pZq`
- **Timezone:** Asia/Kolkata (IST, UTC+5:30)

---

## Accounts Table — Fields to Pull

| Field | ID | Purpose |
|---|---|---|
| Account Name | `fldOSLvopNOX6ae3Z` | Primary identifier |
| Activity notes | `flddz3lqUmEhLhmN5` | Free-text running log — parse for inline dates |
| ACV | `fldjieKzPumeF6afD` | Annual contract value — round to nearest k |
| Renewal Date | `fldPmw5pHDNDgZYgA` | Used for Renewal Radar section |
| Last Activity Date | `fld2jD1HJm9RRwNBW` | Filter for recently touched accounts |

> Do NOT pull Stage or Churn Risk — not used in this skill.

---

## Activity/Notes Table — Fields to Pull

| Field | ID | Purpose |
|---|---|---|
| Notes | `fldcfsEsMgFHoB8VH` | Detailed activity content |
| Title | `fldbf738tn5U18z7D` | Activity title |
| Account | `fldkJTerhbTfcObzR` | Linked account (reliable) |
| Activity Type | `fldfi0aH7CncdNtb9` | Meeting Notes, Account Update, etc. |
| Date | `fld5Dd0gO8vHadVjl` | Primary date filter for weekly scope |
| Next Steps | `flduD4nsj6ZkEXmLZ` | Used when available — not always filled in |

---

## Workflow

### Step 1 — Determine the Target Week

- Default to the current work week (Monday–Friday, IST).
- If the user says "last week" or gives a specific range, use that instead.
- Calculate: Monday date, Friday date, ISO week number, and today's date for the header.

---

### Step 2 — Pull from Activity/Notes Table

Use `list_records_for_table` on `tblI5cCnIY63S6pZq`.

Filter by the `Date` field (`fld5Dd0gO8vHadVjl`) falling within Monday–Friday of the target week.

Pull: Notes, Title, Account, Activity Type, Date, Next Steps.

Paginate using the cursor parameter if needed — do not stop at the first page.

Each row is a discrete activity entry. Use directly — no date parsing needed here.

---

### Step 3 — Pull from Accounts Table

Use `list_records_for_table` on `tblr6UnvfaqfNvwyU`.

Filter by `Last Activity Date` (`fld2jD1HJm9RRwNBW`) falling within the target week to identify recently touched accounts.

Pull: Account Name, Activity notes, ACV, Renewal Date, Last Activity Date.

---

### Step 4 — Parse Inline Dates from Activity Notes (Accounts Table)

The `Activity notes` field is a free-text running log with multiple weeks of history. Extract only entries dated within the target week.

**Typical date formats found:**
- `9 march : note text`
- `10 march 26 : note text`
- `25 march : Met with...`
- `DDmarch` or `DD mar` compact variants

**Parsing rules:**
- Split by line
- Match date patterns: `DD month`, `DD month YY`, `DD month YYYY`
- Match month names case-insensitively (jan/january, feb/february, etc.)
- Assume current year if no year specified
- Include only entries whose parsed date falls within Monday–Friday of the target week
- Continuation lines (no date marker) belong to the preceding dated entry
- If a date is genuinely ambiguous, skip it and note this to the user at the end

---

### Step 5 — Merge Evidence by Account

An account may appear in both tables. Merge all evidence before evaluating.

For each account:
- Combine Activity/Notes rows + parsed Accounts Activity notes entries
- Deduplicate overlapping content
- Keep Next Steps when present — they signal forward momentum
- Prioritize substantive updates over administrative noise

---

### Step 6 — Enrich with Metadata

**ACV:** Round to nearest thousand. Display as `XXk`.
- Example: $87,490 → `87k`

**Renewal Date:** Convert to quarter format `QX YY`.
- Jan–Mar → Q1 | Apr–Jun → Q2 | Jul–Sep → Q3 | Oct–Dec → Q4
- Example: 2026-05-31 → `Q2 26`

**Renewal Radar filter:** Flag accounts whose Renewal Date falls within 90 days of today.

---

### Step 7 — Select Accounts Worth Manager Visibility

Do not include every touched account. Be selective. Include an account if:

**Positive signal (progress worth sharing):**
- A meaningful meeting happened and moved something forward
- A blocker or support issue was resolved
- A commercial or renewal conversation progressed
- Stakeholder engagement improved
- Implementation or adoption moved forward
- A decision was made or next steps were clearly agreed

**Risk signal (manager needs awareness):**
- No response despite follow-ups
- Meeting postponed or customer going dark
- Renewal approaching but no traction
- Issue unresolved and account stalled
- Frustration or negative sentiment surfaced
- Momentum has clearly dropped

**Exclude if:**
- Account is stable and healthy with no notable movement
- Only administrative updates occurred
- Activity was routine with no signal in either direction

Target: 3–6 accounts. More only if there is genuine signal for each.

---

### Step 8 — Build the Renewal Radar

Pull all accounts whose `Renewal Date` falls within 90 days of today's date.

For each:
- Check if they appear in this week's activity
- If yes: describe current momentum in one line
- If no: note that the account is renewing soon but had no activity this week

Sort by renewal date ascending (soonest first).

---

### Step 9 — Write the Portfolio Trend Signal

Look across all accounts touched this week and identify 1–2 patterns. Examples:

- "Three accounts flagged capacity or resource concerns this week"
- "Two implementation stalls are blocked on the same integration issue"
- "Most accounts touched this week are in active renewal conversations"
- "Customer engagement was strong across the board — most meetings had clear outcomes"

If no pattern is visible, omit this section rather than force one.

---

### Step 10 — Write the Report

**Tone:**
- Confident, direct, manager-facing
- Written as if Ranjodh is briefing their manager verbally
- Not overly formal — natural and clear
- No filler phrases. No bullet padding. No "it is worth noting that..."

---

**Report Format:**

```text
Week [ISO_WEEK_NUMBER] | [DAY_OF_WEEK], [FULL_DATE] | [Month Day] – [Month Day], [Year]

Portfolio Pulse
[3–4 sentence overview. How many accounts touched. Overall health of the book this week. Any notable themes. One sentence on what you'd flag as the top story of the week.]

Accounts Worth Manager Visibility
* [Account Name] – [ACV]k – [QX YY] – [One concise narrative sentence on what happened, why it matters, or what risk it signals] - Ranjodh
* [Account Name] – [ACV]k – [QX YY] – [Narrative] - Ranjodh

Renewal Radar (Next 90 Days)
* [Account Name] – [ACV]k – Renewing [QX YY] – [One line on current momentum or lack of activity] - Ranjodh
* [Account Name] – [ACV]k – Renewing [QX YY] – [Narrative] - Ranjodh

Portfolio Trend
[1–2 sentences on patterns observed across accounts this week. Omit section if no clear pattern.]
```

**Formatting rules:**
- Always append ` - Ranjodh` at the end of each account line
- Use `–` (em dash) as separator between account fields
- Keep each account line to one narrative sentence
- Do not use sub-bullets, labels like "Risk:" or "Signal:", or nested formatting
- If Renewal Radar has no accounts, omit that section entirely
- If Portfolio Trend has no signal, omit that section entirely

---

### Step 11 — Deliver via message_compose_v1

Always use `message_compose_v1` with:
- `kind`: `"other"`
- `summary_title`: `"Manager Weekly Recap — Week [#], [Month Day–Day], [Year]"`
- Single variant labeled: `"Manager Weekly Recap"`

Do not output as HTML, artifact, file, or visualizer widget. The copyable text box is the only valid output format.

---

## Edge Cases

**No activity found this week:**
Tell the user no account activity was detected for the target week and ask if they want a different range.

**Activity found but nothing qualifies for manager visibility:**
Tell the user that activity was found but none of it crossed the threshold for manager visibility under this skill's logic. Offer to show everything or lower the bar.

**No renewals in the next 90 days:**
Omit the Renewal Radar section entirely.

**Next Steps field is empty:**
Do not treat absence of Next Steps as a negative signal. Many activities are valid without it. Use the Notes and Title to assess momentum instead.

**Account appears only in Accounts table, not Activity/Notes:**
Still include it if the parsed inline entry from Activity notes is substantive enough to qualify.

**Ambiguous inline date in Activity notes:**
Skip the entry and note the ambiguity to the user briefly at the end of the output.

**Mixed signals on an account:**
Classify based on the dominant direction. If genuinely 50/50, lean toward inclusion in manager visibility if a renewal is approaching, otherwise omit.
