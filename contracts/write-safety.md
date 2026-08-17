# Read, Draft, and Write Safety Contract

Apply `contracts/tool-access-safety.md` before any external connector call. Read/Draft/Write mode controls the permitted operation after the system itself has been authorized; it does not grant blanket connector access.

Every skill must operate in one of four modes.

- Read: retrieve and analyze only from sources authorized by the routed workflow and `contracts/tool-access-safety.md`.
- Draft: create copy or recommendations only from authorized sources.
- Write: update the named system because the explicit command authorizes it.
- Conditional write: read by default and write only after an explicit action request.

## Read Access Is Access

Searching, listing, fetching, inspecting, summarizing, or retrieving data from a connected application counts as accessing that system.

A Read or Draft workflow may not inspect an unrelated connected application merely because the action is non-destructive. The top-level routed skill's source boundary and `contracts/tool-access-safety.md` determine which systems may be read.

## Default Modes

- Docs Assistant, Meeting Prep: Read
- Account Adoption Strategy: Read / Draft; Conditional Notion-only write for an explicit save, publish, push, or update request; V1 never writes Airtable
- Cadence Coverage Radar: Read
- Account Follow-Up Builder: Read / Draft
- Support Ticket, Meeting Follow-Up Email, Clari, SF Stage Validator, weekly reports: Draft/Read
- PS Adoption Audit: Read / Draft; never submit a PS request or modify customer systems
- Update Notes: Write
- Meeting Summarizer: Write when invoked through its explicit registered command; preview when the user asks only to summarize or draft
- Customer Task Centre: Read by default; Conditional Airtable-only write for explicit Customer Task create/update/close/reopen/cancel requests
- Weekly Command Centre: Read by default; Conditional source-aware write only after an explicit apply/close request
- CSM Sentiment Notes: Draft/Preview by default; Conditional write only after a complete preview, exact destination mapping, and fresh explicit apply confirmation

Customer Task Centre never writes Notion. Generic task language does not authorize Notion access or mutation.

## Draft Completion Boundary

A Draft workflow is successfully complete when it produces the requested copy, recommendation, ticket, reply, message, or other draft inside ChatGPT according to the routed skill.

When the user's current request does **not** explicitly ask for an external action:

- Complete the draft and stop.
- Do not attempt the corresponding external write.
- Do not probe, inspect, test, or search for connector availability merely to report whether an external write could be performed.
- Do not mention that a connector is unavailable, unsupported, disconnected, or missing.
- Do not say that the draft `could not be sent`, `could not be submitted`, `could not be posted`, `could not be raised`, or equivalent.
- Do not frame a complete draft as partial or failed because no external action occurred.

Connector availability is a material limitation only when the user's requested outcome actually requires that external action.

Words that describe content creation do not automatically authorize a write. For example, `draft`, `write`, `prepare`, and generic phrases such as `create a support ticket` or `make an email` remain Draft intent when the routed workflow is Draft by default.

External-action intent must be explicit in the current turn and identify the operation clearly, for example:

- `send this email`
- `post this to Slack`
- `submit this ticket to Support`
- `raise this support case`
- `schedule the invite`
- `publish this to Notion`
- `create this draft in Gmail`
- `create this case in [named external system]`

If explicit external-action intent exists, then and only then may the workflow evaluate whether that exact operation is authorized and supported. If it cannot be performed, state the specific limitation concisely after completing any useful draft that still satisfies part of the request. Never claim success without observed write evidence.

A prior-turn request or approval for an external action does not persist into a new Draft workflow run unless the current turn explicitly continues that same unresolved action.

## Account Adoption Strategy Notion Writes

Account Adoption Strategy may publish only to the canonical `6 sense → Customer Plans` hierarchy defined in `schema/notion-customer-plans-map.md`.

Before a write:

1. Require explicit current-turn language such as `save`, `publish`, `push to Notion`, `update in Notion`, or `refresh and save`.
2. Resolve the exact Airtable account.
3. Resolve the exact Notion Customer Plans hub and exact account parent.
4. Fetch any existing `Internal Adoption Strategy — Current` and `Customer-Safe Adoption Plan — Current` pages.
5. Prevent duplicate account parents and duplicate current plan pages.
6. Preserve the internal/customer-safe content boundary.
7. Skip normalized no-change updates.

After a write:

1. Fetch the account parent and every changed current plan page.
2. Report each page as Created, Updated, Unchanged, or Failed.
3. Return only observed Notion URLs.
4. State explicitly that Airtable Customer Tasks and account fields were not changed.
5. Do not claim the plan was shared with the customer.

Publication of a plan does not authorize creation, update, completion, cancellation, or reopening of Airtable Customer Tasks. A later explicit task action must route through Customer Task Centre.

## Bulk CSM Sentiment Writes

A portfolio sentiment request must first produce a complete preview, including every eligible account exactly once.

Before any write:

1. Produce and show the complete preview with the mutually exclusive draft-status counts: Ready, Needs CSM Review, and Insufficient Evidence.
2. Resolve the exact destination system, object/table, record, field ID, and overwrite behavior.
3. Support overwrite only in V1. If the destination is append-only, remain in Draft/Preview until a stable block/source identity and append-specific duplicate check are defined.
4. Fetch the current destination value for every target record.
5. After preview and destination resolution, require a fresh explicit apply confirmation for the reviewed preview or named approved subset.
6. Skip normalized no-change values and report them as Unchanged.
7. Write only the CSM Sentiment destination field; never substitute Activity notes, Detailed Notes, tasks, status, cadence, or risk fields.
8. Assign every eligible target exactly one final outcome: Updated, Unchanged, Not approved, Unmapped, or Failed.
9. Reconcile `Eligible = Updated + Unchanged + Not approved + Unmapped + Failed`.
10. Report every Not approved, Unmapped, or Failed target by account and reason; never imply a complete portfolio update when the equation does not reconcile.

If the CSM Sentiment destination is missing, remain in Draft/Preview mode. A missing authoritative health mapping does not block writing reviewed note text with a clearly labeled CSM recommendation, but it does block claiming an official score/color or updating a separate health field.

## Draft Is Not Sent

Creating or editing an email, Slack message, support ticket, calendar response, customer reply, or Notion customer plan does not prove it was sent, posted, submitted, scheduled, raised, shared, or delivered.

Drafting email copy in ChatGPT also does not authorize Gmail search, thread retrieval, Gmail draft creation, recipient lookup, or sending. Those operations require separate current-turn authorization under `contracts/tool-access-safety.md`.

Treat an action as completed only when the user or trusted source explicitly says it happened, for example:

- `I sent the email`
- `The invite has been scheduled`
- `I raised the support ticket`
- `This was shared with the customer`

Requests such as `write this and update notes` do not prove delivery. Log the underlying promise/task, but do not log a sent touch or close the delivery task until explicit completion evidence appears.

## Reporting Workflows

Reporting and preparation workflows must not silently read from or modify Airtable, Notion, Salesforce, Clari, Slack, Gmail, or other systems outside the source and action boundaries of the routed workflow.

Show recommended changes separately. Apply them only when the user explicitly asks to apply, update, close, reopen, send, post, publish, or submit them.

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
