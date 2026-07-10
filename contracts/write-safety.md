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
- Customer Task Centre: Read by default; Conditional write for create/update/close/reopen/cancel requests
- Weekly Command Centre: Read by default; Conditional write only after an explicit apply/close request

## Draft Is Not Sent

Creating or editing an email, Slack message, support ticket, calendar response, or customer reply does not prove it was sent, posted, submitted, scheduled, or raised.

Treat an action as completed only when the user or trusted source explicitly says it happened, for example:

- `I sent the email`
- `The invite has been scheduled`
- `I raised the support ticket`
- `This was shared with the customer`

Requests such as `write this and update notes` do not prove delivery. Log the underlying promise/task, but do not log a sent touch or close the delivery task until explicit completion evidence appears.

## Reporting Workflows

Reporting and preparation workflows must not silently modify Airtable, Salesforce, Clari, Slack, Gmail, or other systems.

Show recommended changes separately. Apply them only when the user explicitly asks to apply, update, close, reopen, send, post, or submit them.

## Confirmation

After any write, report:

- System updated
- Account/record affected
- What changed
- Any item left in Needs Review
- Any ambiguity that prevented a write
