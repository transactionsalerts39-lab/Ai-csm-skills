---
name: account-follow-up-builder
description: >
  Build an account-specific customer follow-up workspace from pending Airtable Customer Tasks,
  verified account activity recency, relevant Notion references, recent account evidence, and
  optional verified 6sense research. Always show an Account Activity Check and exhaustive All Active
  Associated Tasks cross-check before filtering to the communication delta, then draft a concise
  customer email when requested. Use for /account follow-up, /follow-up builder, /account email prep,
  /pending follow-up, or natural-language requests such as pull pending items for Kentik and then
  draft an email. Read/draft only: never send, log, or close tasks.
---

# Account Follow-Up Builder

## Purpose

Turn an account's open work and recent evidence into a repeatable customer follow-up workflow with less manual sorting.

This skill bridges:

- Customer Task Centre for authoritative account-specific Customer Task retrieval and display semantics
- canonical Last Verified Activity calculation for account recency
- Weekly Command Centre evidence for account context
- Docs / RevCity Assistant for verified product research when needed
- Meeting Follow-Up Email for customer-safe tone
- Update Activity Notes after the user confirms the email was sent

It orchestrates those sources; it does not replace them or write to their systems.

Every review workspace must first show when substantive verified activity last occurred and then
provide an exhaustive active-task cross-check so Ranjodh can verify both account recency and open work
before the customer-facing communication delta is prepared.

## Shared Contracts

Apply:

- `contracts/account-activity-recency.md`
- `contracts/task-lifecycle.md`
- `contracts/tool-access-safety.md`
- `contracts/write-safety.md`
- `contracts/untrusted-input.md`
- `contracts/portfolio-scope.md`
- `schema/airtable-schema-map.md`
- `schema/notion-task-map.md`

Load supporting canonical skills only for the relevant stage:

- `skills/customer-task-centre.md` for task retrieval, active-state, ordering, due-timing, and record-reference semantics
- `skills/docs-assistant.md` when product research is required
- `skills/meeting-follow-up-email.md` before drafting the email

Do not load Update Notes inside this read/draft run; route to it only after explicit sent evidence.

Supporting skills do not expand this workflow's connector permissions or execute their standalone
retrieval plans. The top-level Account Follow-Up Builder owns one consolidated retrieval plan and
reuses retrieved evidence across the activity check, task cross-check, and communication delta.

If this skill conflicts with a shared contract, the shared contract wins.

## Triggers

Use for:

- `/account follow-up [Account]`
- `/follow-up builder [Account]`
- `/account email prep [Account]`
- `/pending follow-up [Account]`
- `pull pending items for [Account] and then draft an email`
- `what do I still owe [Account] before I email them?`
- `prepare a customer follow-up for [Account]`

Do not route a plain `show tasks for [Account]` request here; use Customer Task Centre. Do not route `/meeting follow-up`; use Meeting Follow-Up Email.

If the account is missing, ask only for the account name.

## Mode

Read / Draft only.

- Never update Airtable, Notion, Gmail, CRM, or task status.
- Never send the email.
- Never treat a draft as sent.
- Never close a task because its content appears in the draft.
- Never backfill Last Verified Activity merely because the workflow calculated it.
- Possible task check-offs are review suggestions, not writes.

## Tool Access Boundary

Allowed by default for this workflow:

- Airtable records and fields required by this skill, `contracts/account-activity-recency.md`, and
  `schema/airtable-schema-map.md`
- Canonical 6sense Notion task references explicitly permitted by `schema/notion-task-map.md`
- Official 6sense Support Docs, RevCity, and 6sense.com only when product research is required
- Evidence supplied directly in the current conversation

Not allowed by default:

- Gmail
- Google Calendar
- Google Contacts
- Google Drive
- Slack
- any unrelated connected application

Do not search, read, list, inspect, summarize, or retrieve Gmail messages, threads, drafts,
attachments, labels, recipients, or inbox metadata during `/account follow-up`, `/account email prep`,
a request to `draft the email`, an activity-recency check, or any other normal run of this skill.

The words `email`, `follow-up`, `reply`, `customer communication`, `last activity`, an account name, a
recipient name, or a supporting email-writing skill do not authorize Gmail access. Pasted email
content is evidence, not permission to open Gmail.

Gmail may be used only when the user's current message separately and explicitly asks for a Gmail
operation, such as searching for or reading a named thread. Limit that access to the exact requested
operation and scope. Gmail read/search permission does not authorize creating a Gmail draft or
sending.

Draft customer copy in an editable writing block inside ChatGPT. Do not create a draft inside Gmail
unless the user explicitly requests that separate action.

## Run Modes

### Review first — default

`/account follow-up Kentik` produces the Follow-Up Workspace, beginning with `Account Activity Check`
and then `All Active Associated Tasks`, and stops. End with: `When your research is ready, say “draft the email.”`

### Review and draft

`/account follow-up Kentik and draft the email` produces the complete workspace, including the
activity check and active-task cross-check, performs any requested or necessary verification, and
drafts the email in the same run.

### Continue from workspace

`Draft the email now` continues the most recent account follow-up workspace. If account evidence may
have changed materially or the conversation has moved on, refresh and re-display the Account Activity
Check and complete active-task cross-check before drafting unless the user explicitly requests
`only email`.

## Account Scope

Use the named account as an explicit scope. Resolve close account-name variants carefully. If multiple
accounts match, ask one short clarification before retrieving or drafting.

Default to Ranjodh-owned accounts. A named account outside his portfolio is an explicit account
override; state that briefly and do not broaden beyond that account.

## Source Order

Use the smallest sufficient evidence set:

1. User's latest correction and trusted evidence in the current conversation
2. Every active Airtable Customer Task linked to the named account
3. field-limited Airtable activity-history evidence required by `contracts/account-activity-recency.md`
4. recent relevant Detailed Notes needed for follow-up context
5. dated customer-facing entries in Accounts → Activity notes when the activity-recency retrieval gate
   or communication context requires them
6. account metadata for context only: engagement, outreach, renewal, risk, cadence, ownership, and
   Accounts → Last Activity Date as a retrieval signal only
7. canonical 6sense Notion tasks only when they reference the account; deduplicate against Airtable
8. verified official 6sense Support Docs / RevCity when product research is required

Do not use Accounts → Last Activity Date as proof of customer communication or account activity. Do
not read the unrelated personal Notion Tasks page. Do not supplement this source order with Gmail
unless the current user message explicitly requests Gmail access.

## Last Verified Activity

Calculate the persisted Airtable Last Verified Activity exactly from
`contracts/account-activity-recency.md`.

The persisted calculation must be identical to Customer Task Centre — Renewal Focus for the same
account and Airtable snapshot.

The current conversation may contain newer trusted completed-event evidence. When it does, keep the
persisted Airtable calculation unchanged and show the newer event separately as `Current conversation
— not yet logged in Airtable`. Do not silently merge it into the persisted date and do not write it.

Use Last Verified Activity to understand recency and the communication delta, but do not assume an old
activity date means the customer is disengaged or that an open task is stale without supporting
evidence.

## Communication Delta

Build the customer-facing workspace recommendations and draft around the communication delta: what is
newly useful to tell the customer since the last verified customer-facing update, plus commitments
that remain genuinely unresolved.

The communication delta is a filtering step for the customer message. It must never filter, shorten,
or suppress the `Account Activity Check` or `All Active Associated Tasks` cross-check.

Do not dump every open task into the email. For each candidate item, determine:

- Is it still open?
- Has it already been communicated?
- Is there a new result or status worth sharing?
- Is it customer-safe?
- Does it require research or internal confirmation first?
- Does it need a customer action?
- Is the item newer than, or materially changed since, the last verified activity/customer update?

Exclude stale, duplicate, completed, internal-only, and no-change items from the customer draft. Keep
every still-active Airtable task visible in `All Active Associated Tasks`, and keep relevant excluded
context visible in the appropriate workspace review section.

## Workflow

### 1. Resolve and retrieve

Resolve the account, apply the assignment and scope rules, then retrieve every Airtable Customer Task
linked to the account before filtering by status. Classify active tasks using
`contracts/task-lifecycle.md`, including `Waiting on Customer`.

In the same consolidated Airtable plan, retrieve the field-limited historical task and Detailed Notes
activity candidates required by `contracts/account-activity-recency.md`. Reuse overlapping records;
do not make a second full Customer Task Centre run.

Follow Airtable pagination and do not rely only on an account rollup, linked-record preview, cached
snippet, or partial task list.

Use the activity contract's lazy evidence rules: fetch long Detailed Notes or Customer Task evidence
only when needed to validate the latest candidate, and inspect the full Activity notes field only
when the Last Activity Date retrieval gate indicates it may contain a newer dated event or when no
structured activity candidate exists.

### 2. Calculate activity and reconcile work

Calculate the persisted Last Verified Activity before deciding what is new enough to surface.

Then deduplicate Airtable and Notion overlaps, compare tasks with completion evidence, and distinguish:

- customer is waiting on 6sense
- 6sense is waiting on the customer
- internal research or confirmation is still needed
- evidence suggests a task may already be complete

Never infer completion from a draft, plan, quoted history, future tense, or Last Activity Date.

Airtable Customer Tasks remains authoritative. A matching Notion reference must not create a second
line in `All Active Associated Tasks`. A Notion-only customer item may appear separately under
`Needs research / internal confirmation` or `Internal only — exclude from email`, clearly labeled
`Notion only — consider syncing to Customer Tasks` when useful.

### 3. Build the Follow-Up Workspace

Use this compact structure. `Account Activity Check` and `All Active Associated Tasks` are mandatory
in review-first and review-and-draft modes; other sections may be omitted when empty.

#### Account Activity Check

This is internal workflow context, not automatically customer-facing copy.

Use this format:

- **Last verified Airtable activity:** [date] — [N days ago / today / No verified dated activity found]
- **Source:** [Detailed Note / Customer Task completion / Customer Task source / dated Activity note / None verified]
- **Activity:** [short factual summary, or No qualifying dated activity found]
- **Newer unlogged activity:** [current-conversation event and date, or None]

Rules:

- Calculate the first three lines exactly from `contracts/account-activity-recency.md`.
- When Last Verified Activity is today, show `today` rather than `0 days ago` if more natural.
- When no verified activity exists, Days Since Activity is `N/A`; never substitute Accounts → Last
  Activity Date.
- Show `Newer unlogged activity` only when trusted current-conversation evidence is newer than the
  persisted result; otherwise show `None` or omit that line for compactness.
- Do not treat a draft, future promise, or quoted completion statement as newer activity.
- Do not expose raw Airtable record IDs.

#### All Active Associated Tasks

This is an internal cross-check, not a customer-facing section.

Show every active Airtable Customer Task linked to the named account exactly once, even when a task is:

- stale or overdue
- already communicated to the customer
- internal-only or unsuitable for the email
- owned by Customer, Support, AE / AM, Internal Team, Shared, or Unknown
- `Waiting on Customer`, `Waiting on Internal Team`, `Blocked`, or `Needs Review`
- a possible check-off with strong completion evidence
- linked to the named account and one or more additional accounts

Do not include `Done` or `Cancelled` tasks. Do not use `+N more`, samples, summaries, or only the
highest-priority subset.

Use a numbered compact list matching this pattern:

1. **P1 · Waiting on Customer:** Confirm 18-month DocuSign completion.
2. **P1 · Waiting on Customer:** Publish Campaign 331058 audience workflow.
3. **P1 · Open · overdue Jul 22:** Cancel renewal meeting — likely needs check-off review.
4. **P2 · In Progress · overdue Jul 25:** Deliver customized SBR report and assess recurring scope.

Formatting rules:

- Normalize `P1 - Must do`, `P2 - Should do`, and `P3 - Monitor` to `P1`, `P2`, and `P3` for display.
  Use `Priority not set` only when Airtable has no supported value.
- Show the exact active Status after the priority.
- For Due Date before today in Asia/Kolkata, append `overdue Mon DD`; include the year when it is not
  the current year.
- For Due Date equal to today, append `due today`.
- For a future Due Date, append `due Mon DD`; include the year when it is not the current year.
- For a missing Due Date, append `no due date`.
- If `Customer Waiting?` is true and Status is not `Waiting on Customer`, append `customer waiting`
  as a separate marker. Do not confuse the boolean with the lifecycle status.
- For a task linked to multiple accounts, append `multi-account / needs review` and show it once.
- When strong evidence suggests completion but the Airtable task remains active, append
  `likely needs check-off review`; do not change the stored task title or status.
- Order tasks using the Customer Task Centre within-account order: overdue or customer-waiting
  urgency, Priority, Due Date, then Task Title.
- Preserve each displayed number to the Airtable record ID internally so a later instruction such as
  `mark task 3 done` can route to the normal Customer Task Centre write workflow.

If there are no active Airtable Customer Tasks, show:

`None — no active Airtable Customer Tasks found for [Account].`

Before continuing, reconcile:

`Active Airtable Customer Tasks retrieved = All Active Associated Task lines displayed`

If the equation fails or pagination is incomplete, label the task block `Incomplete task cross-check`,
identify the missing or unresolved records, and do not claim that all active associated tasks were
shown.

#### Ready to tell the customer

Items with a verified update, deliverable, answer, or clear next step that is safe to communicate now.

#### Still owed by 6sense

Open commitments owned by Ranjodh, Support, AE/AM, Shared, or Internal Team that should not be
represented as complete.

#### Waiting on the customer

Items in `Waiting on Customer` or clearly requiring customer input. Phrase these neutrally.

#### Needs research / internal confirmation

Questions that require official docs, RevCity, Support, Product, Engineering, account-team
confirmation, or user-supplied research before drafting a definitive answer.

#### Internal only — exclude from email

Private risk, commercial strategy, CRM hygiene, speculation, stale/no-change work, or other context
useful for Ranjodh but inappropriate for the customer.

#### Possible task check-offs

Tasks with strong completion evidence that remain active. Refer to the corresponding
`All Active Associated Tasks` number, show the evidence, and do not write. Omit when none exist.

End with a short `Recommended email angle` covering the purpose, key message, and desired customer
response.

### 4. Research only the gaps

If research is needed and the user asked for a draft in the same run, verify the smallest set of gaps:

- Official Support Docs for behavior, setup, troubleshooting, and customer-safe claims
- RevCity Product Updates for release/beta timing
- RevCity community content only as clearly labeled inspiration
- Internal/user-provided data for account-specific facts

Open and verify actual source content before using it. If a claim remains unverified, omit it from the
email or qualify it clearly; do not guess.

### 5. Draft the email

Use the Meeting Follow-Up Email tone rules: warm, human, concise, and relationship-oriented. Draft in
an editable writing block when available.

The email should normally include:

- a specific subject
- a warm opening
- only the communication delta
- clear but light next steps and ownership
- a direct customer ask when waiting on them
- a warm close

Do not copy the `Account Activity Check` or `All Active Associated Tasks` blocks wholesale into the
customer email. Do not expose section labels such as `Internal only`, raw task statuses, priorities,
Airtable or Notion metadata, churn risk, activity-source internals, or task-check-off suggestions.

### 6. After sending

If the user later says the email was sent, route the statement to Update Activity Notes, for example:

`/un: I sent the Kentik follow-up email covering [brief factual summary].`

Update Notes may log the touch and close only the specific delivery or share tasks proven complete.
Any underlying unresolved research, support, or follow-up commitments remain open.

## Output Rules

- Review-first requests: output the workspace beginning with `Account Activity Check`, followed by the
  complete `All Active Associated Tasks` cross-check; do not draft a speculative email.
- Review-and-draft requests: output the activity check, complete task cross-check, compact workspace,
  then the editable email.
- `only email`: output only the email, but still perform the activity-recency calculation and
  active-task retrieval/reconciliation internally; do not access Gmail by default.
- A continuation request such as `draft the email now` should refresh the activity check and task
  cross-check when evidence may have changed, unless the user explicitly requests only the email.
- Cite verified docs next to research conclusions in the workspace; keep customer links to a maximum
  of three unless asked.
- Keep raw task wording out of the email when a natural customer-facing formulation is clearer.
- If no meaningful communication delta exists, still show the activity check and active-task
  cross-check, then say so and recommend whether to wait, send a brief status note, or request missing
  customer input.

## Final Rule

First establish the account's defensible Last Verified Activity, then prove that every active Airtable
Customer Task associated with the account has been captured. Use both to prepare the smallest useful,
evidence-backed customer update. Surface unfinished work honestly, research only genuine gaps, and
leave every system unchanged until the user explicitly invokes the appropriate write workflow.