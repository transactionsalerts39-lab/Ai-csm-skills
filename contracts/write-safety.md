# Read, Draft, and Write Safety Contract

Every skill must operate in one of four modes.

- Read: retrieve and analyze only.
- Draft: create copy or recommendations only.
- Write: update the named system because the explicit command authorizes it.
- Conditional write: read by default and write only after an explicit action request.

## Default Modes

- Docs Assistant, Meeting Prep: Read
- Support Ticket, Meeting Follow-Up Email, Clari, SF Stage Validator, weekly reports: Draft/Read
- Update Notes: Write
- Meeting Summarizer: Write when invoked through its explicit registered command; preview when the user asks only to summarize or draft
- Customer Task Centre: Read by default; Conditional source-aware write for create/update/close/reopen/cancel requests in Airtable or the canonical 6sense Notion task page
- Weekly Command Centre: Read by default; Conditional source-aware write only after an explicit apply/close request

## Draft Is Not Sent

Creating or editing an email, Slack message, support ticket, calendar response, or customer reply does not prove it was sent, posted, submitted, scheduled, or raised.

Treat an action as completed only when the user or trusted source explicitly says it happened, for example:

- `I sent the email`
- `The invite has been scheduled`
- `I raised the support ticket`
- `This was shared with the customer`

Requests such as `write this and update notes` do not prove delivery. Log the underlying promise/task, but do not log a sent touch or close the delivery task until explicit completion evidence appears.

## Reporting Workflows

Reporting and preparation workflows must not silently modify Airtable, Notion, Salesforce, Clari, Slack, Gmail, or other systems.

Show recommended changes separately. Apply them only when the user explicitly asks to apply, update, close, reopen, send, post, or submit them.

## Idempotent Ingestion

Before appending an Activity note or creating a Detailed Notes record, check whether the same source was already logged for the same account.

Use a source identity in this order:

1. External message, meeting, transcript, or source record ID when available
2. Same account + source type + source date + normalized source content
3. Strong semantic match against the most recent relevant note/record

On a repeated source:

- Do not append a duplicate Activity note.
- Do not create a duplicate Detailed Notes record.
- Do not create a duplicate Customer Task.
- Reconcile genuinely new status, due-date, owner, or completion evidence only.
- Confirm that the source was already logged and no duplicate was added.

If the source is similar but not clearly identical, do not suppress it silently; mark the ambiguity or ask one concise question.

## Confirmation

After any write, report:

- System updated
- Account/record affected
- What changed
- Any item left in Needs Review
- Any ambiguity that prevented a write
