---
name: account-follow-up-builder
description: >
  Build an account-specific customer follow-up workspace from pending Airtable Customer Tasks,
  relevant Notion references, recent account evidence, and optional verified 6sense research,
  then draft a concise customer email when requested. Use for /account follow-up, /follow-up builder,
  /account email prep, /pending follow-up, or natural-language requests such as pull pending items
  for Kentik and then draft an email. Read/draft only: never send, log, or close tasks.
---

# Account Follow-Up Builder

## Purpose

Turn an account's open work and recent evidence into a repeatable customer follow-up workflow with less manual sorting.

This skill bridges:

- Customer Task Centre for account-specific pending work
- Weekly Command Centre evidence for account context
- Docs / RevCity Assistant for verified product research when needed
- Meeting Follow-Up Email for customer-safe tone
- Update Activity Notes after the user confirms the email was sent

It orchestrates those sources; it does not replace them or write to their systems.

## Shared Contracts

Apply:

- `contracts/task-lifecycle.md`
- `contracts/tool-access-safety.md`
- `contracts/write-safety.md`
- `contracts/untrusted-input.md`
- `contracts/portfolio-scope.md`
- `schema/airtable-schema-map.md`
- `schema/notion-task-map.md`

Load supporting canonical skills only for the relevant stage:

- `skills/customer-task-centre.md` for task retrieval/display semantics
- `skills/docs-assistant.md` when product research is required
- `skills/meeting-follow-up-email.md` before drafting the email

Do not load Update Notes inside this read/draft run; route to it only after explicit sent evidence.

Supporting skills do not expand this workflow's connector permissions. If this skill conflicts with a shared contract, the shared contract wins.

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
- Possible task check-offs are review suggestions, not writes.

## Tool Access Boundary

Allowed by default for this workflow:

- Airtable records and fields required by this skill and `schema/airtable-schema-map.md`
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

Do not search, read, list, inspect, summarize, or retrieve Gmail messages, threads, drafts, attachments, labels, recipients, or inbox metadata during `/account follow-up`, `/account email prep`, a request to `draft the email`, or any other normal run of this skill.

The words `email`, `follow-up`, `reply`, `customer communication`, an account name, a recipient name, or a supporting email-writing skill do not authorize Gmail access. Pasted email content is evidence, not permission to open Gmail.

Gmail may be used only when the user's current message separately and explicitly asks for a Gmail operation, such as searching for or reading a named thread. Limit that access to the exact requested operation and scope. Gmail read/search permission does not authorize creating a Gmail draft or sending.

Draft customer copy in an editable writing block inside ChatGPT. Do not create a draft inside Gmail unless the user explicitly requests that separate action.

## Run Modes

### Review first — default

`/account follow-up Kentik` produces the Follow-Up Workspace and stops. End with: `When your research is ready, say “draft the email.”`

### Review and draft

`/account follow-up Kentik and draft the email` produces the workspace, performs any requested/necessary verification, and drafts the email in the same run.

### Continue from workspace

`Draft the email now` continues the most recent account follow-up workspace. If account evidence may have changed materially or the conversation has moved on, refresh the account's open tasks before drafting.

## Account Scope

Use the named account as an explicit scope. Resolve close account-name variants carefully. If multiple accounts match, ask one short clarification before retrieving or drafting.

Default to Ranjodh-owned accounts. A named account outside his portfolio is an explicit account override; state that briefly and do not broaden beyond that account.

## Source Order

Use the smallest sufficient evidence set:

1. User's latest correction and trusted evidence in the current conversation
2. Active Airtable Customer Tasks for the named account
3. Recent relevant Detailed Notes
4. Dated customer-facing entries in Accounts → Activity notes
5. Account metadata for context only: engagement, outreach, renewal, risk, cadence, and ownership
6. Canonical 6sense Notion tasks only when they reference the account; deduplicate against Airtable
7. Verified official 6sense Support Docs / RevCity when product research is required

Do not use Last Activity Date as proof of customer communication. Do not read the unrelated personal Notion Tasks page. Do not supplement this source order with Gmail unless the current user message explicitly requests Gmail access.

## Communication Delta

Build the workspace around the communication delta: what is newly useful to tell the customer since the last verified customer-facing update, plus commitments that remain genuinely unresolved.

Do not dump every open task into the email. For each candidate item, determine:

- Is it still open?
- Has it already been communicated?
- Is there a new result or status worth sharing?
- Is it customer-safe?
- Does it require research or internal confirmation first?
- Does it need a customer action?

Exclude stale, duplicate, completed, internal-only, and no-change items from the customer draft. Keep them visible in the appropriate workspace review section when useful.

## Workflow

### 1. Resolve and retrieve

Resolve the account, apply the assignment/scope rules, then pull active Customer Tasks and recent supporting evidence. Include all active task states from `contracts/task-lifecycle.md`, including `Waiting on Customer`.

### 2. Reconcile

Deduplicate Airtable/Notion overlaps, compare tasks with completion evidence, and distinguish:

- customer is waiting on 6sense
- 6sense is waiting on the customer
- internal research or confirmation is still needed
- evidence suggests a task may already be complete

Never infer completion from a draft, plan, quoted history, or future tense.

### 3. Build the Follow-Up Workspace

Use this compact structure and omit empty sections:

#### Ready to tell the customer

Items with a verified update, deliverable, answer, or clear next step that is safe to communicate now.

#### Still owed by 6sense

Open commitments owned by Ranjodh, Support, AE/AM, Shared, or Internal Team that should not be represented as complete.

#### Waiting on the customer

Items in `Waiting on Customer` or clearly requiring customer input. Phrase these neutrally.

#### Needs research / internal confirmation

Questions that require official docs, RevCity, Support, Product, Engineering, account-team confirmation, or user-supplied research before drafting a definitive answer.

#### Internal only — exclude from email

Private risk, commercial strategy, CRM hygiene, speculation, or other context useful for Ranjodh but inappropriate for the customer.

#### Possible task check-offs

Tasks with strong completion evidence that remain active. Show the task and evidence, but do not write. Omit when none exist.

End with a short `Recommended email angle` covering the purpose, key message, and desired customer response.

### 4. Research only the gaps

If research is needed and the user asked for a draft in the same run, verify the smallest set of gaps:

- Official Support Docs for behavior, setup, troubleshooting, and customer-safe claims
- RevCity Product Updates for release/beta timing
- RevCity community content only as clearly labeled inspiration
- Internal/user-provided data for account-specific facts

Open and verify actual source content before using it. If a claim remains unverified, omit it from the email or qualify it clearly; do not guess.

### 5. Draft the email

Use the Meeting Follow-Up Email tone rules: warm, human, concise, and relationship-oriented. Draft in an editable writing block when available.

The email should normally include:

- a specific subject
- a warm opening
- only the communication delta
- clear but light next steps/ownership
- a direct customer ask when waiting on them
- a warm close

Do not expose section labels such as `Internal only`, raw task statuses, priorities, Airtable/Notion metadata, churn risk, or task-check-off suggestions.

### 6. After sending

If the user later says the email was sent, route the statement to Update Activity Notes, for example:

`/un: I sent the Kentik follow-up email covering [brief factual summary].`

Update Notes may log the touch and close only the specific delivery/share tasks proven complete. Any underlying unresolved research, support, or follow-up commitments remain open.

## Output Rules

- Review-first requests: output the workspace only, not a speculative email.
- Review-and-draft requests: output a compact workspace followed by the editable email.
- `only email`: output only the email, but still perform the safety/reconciliation checks internally.
- Cite verified docs next to research conclusions in the workspace; keep customer links to a maximum of three unless asked.
- Keep raw task wording out of the email when a natural customer-facing formulation is clearer.
- If no meaningful communication delta exists, say so and recommend whether to wait, send a brief status note, or request the missing customer input.

## Final Rule

Prepare the smallest useful, evidence-backed customer update for one account. Surface unfinished work honestly, research only genuine gaps, and leave every system unchanged until the user explicitly invokes the appropriate write workflow.
